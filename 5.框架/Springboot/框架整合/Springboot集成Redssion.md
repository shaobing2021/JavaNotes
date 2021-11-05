[官网](https://github.com/redisson/redisson/wiki/2.-%E9%85%8D%E7%BD%AE%E6%96%B9%E6%B3%95)

### 添加依赖

```xml
<dependency>
       <groupId>org.redisson</groupId>
       <artifactId>redisson-spring-boot-starter</artifactId>
       <version>3.11.5</version>
 </dependency>
```

### 配置类

```java
package com.shaobing.config;

import org.redisson.Redisson;
import org.redisson.api.RedissonClient;
import org.redisson.config.Config;
import org.redisson.spring.starter.RedissonAutoConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class RedissonConfig extends RedissonAutoConfiguration {
    @Bean
    public RedissonClient redissonClient(){
        Config config = new Config();
        config.useSingleServer().setAddress("redis://124.70.77.210:6379");
        return  Redisson.create(config);
    }
}
```

```yml
spring.redis.cluster.nodes=124.70.77.210:7001,124.70.77.210:7002,124.70.77.210:7003,124.70.77.210:7004,124.70.77.210:7005,124.70.77.210:7006
#spring.redis.host=124.70.77.210
```

### 原生RedisDao

### 分布式锁

```java
package com.luozheng.Dao;
import com.luozheng.Service.TicketServiceImpl;
import org.apache.commons.lang3.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.util.Assert;
import redis.clients.jedis.JedisCluster;
import redis.clients.jedis.exceptions.JedisNoScriptException;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

@Component
public class RedisLock {

    private Logger LOGGER  = LoggerFactory.getLogger(TicketServiceImpl.class);
    @Autowired
    private JedisCluster jedisCluster;
    private static final ThreadLocal<String> CLIENT_ID = ThreadLocal.withInitial(
            () -> String.format("%s_%s", Thread.currentThread().getName(), UUID.randomUUID().toString())
    );
    private Map<String, ReentrantLock> LOCAL_LOCKS = new ConcurrentHashMap<>();
    private static final String SUCCESS_LOCK = "1";
    private static final Long SUCCESS_UNLOCK = 1L;
    private volatile String unlockSha1 = "";
    private static final String UNLOCK_SCRIPT = "if" +
            " redis.call('get', KEYS[1]) == ARGV[1]" +
            " then" +
            " return redis.call('del', KEYS[1])" +
            " else" +
            " return 0" +
            " end";

    private static final String LOCK_SCRIPT = "if " +
            "redis.call('get',KEYS[1]) == ARGV[1]" +
            " then" +
            " redis.call('EXPIRE',KEYS[1],tonumber(ARGV[2])) return 1" +
            " elseif" +
            " redis.call('SETNX',KEYS[1],ARGV[1]) == 1" +
            " then" +
            " redis.call('EXPIRE',KEYS[1],tonumber(ARGV[2])) return 1" +
            " else return 0" +
            " end";
    @Autowired
    public void setJedisCluster(JedisCluster jedisCluster) {
        this.jedisCluster = jedisCluster;
    }


    public boolean lock(String lockKey, long expireTime, long sleepTime) {
        String clientId = CLIENT_ID.get();
        return lock(lockKey, clientId, expireTime, sleepTime);
    }
    /**
     * 相比之前代码少了本地锁ReentrantLock
     * @param lockKey   锁住的key
     * @param lockValue 锁住的值，用于解锁校验
     * @param expireTime  锁过期时间  毫秒
     * @param sleepTime 重新尝试获取锁   秒
     * @return
     */
    public boolean lock(String lockKey, String lockValue, long expireTime, long sleepTime) {
        Assert.notNull(lockKey, "lockKey为空");
        Assert.notNull(lockValue, "lockValue为空");
        List<String> keyList = new ArrayList<>();
        keyList.add(lockKey);
        List<String> avgList = new ArrayList<>();
        avgList.add(lockValue);
        avgList.add(String.valueOf(expireTime));
        String result = "0";
        while(true){
            try {
                result = jedisCluster.eval(LOCK_SCRIPT, keyList, avgList).toString();
            } catch (Exception e) {
                LOGGER.error("Redis分布式锁加锁异常");
            }
            if (SUCCESS_LOCK.equalsIgnoreCase(result)){
                LOGGER.info("获取锁成功"+lockValue);
                return true;
            }else {
//                LOGGER.info(String.format("锁被占用，重试中..."));
                try {
                    TimeUnit.SECONDS.sleep(sleepTime);
                } catch (InterruptedException e) {
                    LOGGER.error("Redis分布式锁获取被打断...");
                }
            }
        }
    }

    public boolean unlock(String lockKey){

        String clientId = CLIENT_ID.get();
        return unlock(lockKey, clientId);

    }
    /**
     * 解锁代码
     * @param lockKey
     * @param lockVal
     * @return
     */
    public boolean unlock(String lockKey, String lockVal) {
        List<String> keyList = new ArrayList<>();
        keyList.add(lockKey);
        List<String> avgList = new ArrayList<>();
        avgList.add(lockVal);
        try {
            LOGGER.info("准备释放锁"+CLIENT_ID.get()+"--"+unlockSha1);
            Object result = jedisCluster.evalsha(unlockSha1, keyList, avgList);
            if (SUCCESS_UNLOCK.equals(result)){
                LOGGER.info("释放锁成功"+CLIENT_ID.get());
            }
//            LOGGER.info("result:"+result);
        } catch (JedisNoScriptException e) {
            //如果当前节点还没有缓存脚本，再执行一次缓存
            storeScript(lockKey);
            Object result = jedisCluster.evalsha(unlockSha1, keyList, avgList);
            return SUCCESS_UNLOCK.equals(result);
        } catch (Exception e) {
            LOGGER.error( "redis 解锁失败");
        }
        return false;
    }

    private void storeScript(String slotKey) {
        if (StringUtils.isEmpty(unlockSha1) || !jedisCluster.scriptExists(unlockSha1, slotKey)) {
            //redis支持脚本缓存，返回哈希码，后续可以继续用来调用脚本
            unlockSha1 = jedisCluster.scriptLoad(UNLOCK_SCRIPT, slotKey);
        }
    }

    /**
     * 用于获取指定 key 的值
     *
     * @param key
     * @return 返回 key 的值，如果 key 不存在时，返回 nil。 如果 key 不是字符串类型，那么返回一个错误。
     */
    public String get(String key) {
        return jedisCluster.get(key);
    }

    public String set(String key, String value) {
        return jedisCluster.set(key, value);
    }
}
```

### 分布式锁+本地锁

```java
package com.luozheng.Dao;

import com.luozheng.Service.TicketServiceImpl;
import org.apache.commons.lang3.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.util.Assert;
import redis.clients.jedis.JedisCluster;
import redis.clients.jedis.exceptions.JedisNoScriptException;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

@Component
public class RedisDistributeLock {

    private Logger LOGGER  = LoggerFactory.getLogger(TicketServiceImpl.class);
    @Autowired
    private JedisCluster jedisCluster;
    private static final ThreadLocal<String> CLIENT_ID = ThreadLocal.withInitial(
            () -> String.format("%s_%s", Thread.currentThread().getName(), UUID.randomUUID().toString())
    );
    private Map<String, ReentrantLock> LOCAL_LOCKS = new ConcurrentHashMap<>();
    private static final String SUCCESS_LOCK = "1";
    private static final Long SUCCESS_UNLOCK = 1L;
    private volatile String unlockSha1 = "";
    private static final String UNLOCK_SCRIPT = "if" +
            " redis.call('get', KEYS[1]) == ARGV[1]" +
            " then" +
            " return redis.call('del', KEYS[1])" +
            " else" +
            " return 0" +
            " end";

    private static final String LOCK_SCRIPT = "if " +
            "redis.call('get',KEYS[1]) == ARGV[1]" +
            " then" +
            " redis.call('EXPIRE',KEYS[1],tonumber(ARGV[2])) return 1" +
            " elseif" +
            " redis.call('SETNX',KEYS[1],ARGV[1]) == 1" +
            " then" +
            " redis.call('EXPIRE',KEYS[1],tonumber(ARGV[2])) return 1" +
            " else return 0" +
            " end";

    @Autowired
    public void setJedisCluster(JedisCluster jedisCluster) {
        this.jedisCluster = jedisCluster;
    }


    public boolean lock(String lockKey, long expireTime, long sleepTime) {
        String clientId = CLIENT_ID.get();
        return lock(lockKey, clientId, expireTime, sleepTime);
    }
    /**
     * 相比之前代码少了本地锁ReentrantLock
     * @param lockKey   锁住的key
     * @param lockValue 锁住的值，用于解锁校验
     * @param expireTime  锁过期时间  毫秒
     * @param sleepTime 重新尝试获取锁   秒
     * @return
     */
    public boolean lock(String lockKey, String lockValue, long expireTime, long sleepTime) {
        LOCAL_LOCKS.putIfAbsent(lockKey,new ReentrantLock());
        ReentrantLock lock = LOCAL_LOCKS.get(lockKey);
        Assert.notNull(lockKey, "lockKey为空");
        Assert.notNull(lockValue, "lockValue为空");
        List<String> keyList = new ArrayList<>();
        keyList.add(lockKey);
        List<String> avgList = new ArrayList<>();
        avgList.add(lockValue);
        avgList.add(String.valueOf(expireTime));
        lock.lock();
        String result = "0";
        while(true){
            try {
                result = jedisCluster.eval(LOCK_SCRIPT, keyList, avgList).toString();
            } catch (Exception e) {
                LOGGER.error("Redis分布式锁加锁异常");
                lock.unlock();
            }
            if (SUCCESS_LOCK.equalsIgnoreCase(result)){
                LOGGER.info("获取锁成功"+lockValue);
                return true;
            }else {
//                LOGGER.info(String.format("锁被占用，重试中..."));
                try {
                    TimeUnit.SECONDS.sleep(sleepTime);
                } catch (InterruptedException e) {
                    LOGGER.error("Redis分布式锁获取被打断...");
                }
            }
        }
    }

    public boolean unlock(String lockKey){
        String clientId = CLIENT_ID.get();
        return unlock(lockKey, clientId);

    }
    /**
     * 解锁代码
     * @param lockKey
     * @param lockVal
     * @return
     */
    public boolean unlock(String lockKey, String lockVal) {
        ReentrantLock lock = LOCAL_LOCKS.get(lockKey);
        //如果不是当前线程拥有的锁或者当前key的锁没有注册进来，解锁失败
        if (lock == null || !lock.isHeldByCurrentThread()) {
            throw new IllegalStateException(String.format(Thread.currentThread().getName() + "线程lockKey[%s]未持有锁，解锁失败", lockKey));
        }
        //检测到是重入了，holdCount减一，当holdCount等于1时，说明本地锁已经都解锁了，再执行解除redis锁
        if (lock.getHoldCount() > 1) {
            lock.unlock();
            return true;
        }
        List<String> keyList = new ArrayList<>();
        keyList.add(lockKey);
        List<String> avgList = new ArrayList<>();
        avgList.add(lockVal);
        try {
            LOGGER.info("准备释放锁"+CLIENT_ID.get()+"--"+unlockSha1);
            Object result = jedisCluster.evalsha(unlockSha1, keyList, avgList);
            if (SUCCESS_UNLOCK.equals(result)){
                LOGGER.info("释放锁成功"+CLIENT_ID.get());
            }
//            LOGGER.info("result:"+result);
        } catch (JedisNoScriptException e) {
            //如果当前节点还没有缓存脚本，再执行一次缓存
            storeScript(lockKey);
            Object result = jedisCluster.evalsha(unlockSha1, keyList, avgList);
            return SUCCESS_UNLOCK.equals(result);
        } catch (Exception e) {
            LOGGER.error( "redis 解锁失败");
        }finally {
            lock.unlock();
        }
        return false;
    }

    private void storeScript(String slotKey) {
        if (StringUtils.isEmpty(unlockSha1) || !jedisCluster.scriptExists(unlockSha1, slotKey)) {
            //redis支持脚本缓存，返回哈希码，后续可以继续用来调用脚本
            unlockSha1 = jedisCluster.scriptLoad(UNLOCK_SCRIPT, slotKey);
        }
    }
    /**
     * 用于获取指定 key 的值
     *
     * @param key
     * @return 返回 key 的值，如果 key 不存在时，返回 nil。 如果 key 不是字符串类型，那么返回一个错误。
     */
    public String get(String key) {
        return jedisCluster.get(key);
    }
    public String set(String key, String value) {
        return jedisCluster.set(key, value);
    }
}

```

### 测试类

```java
package com.luozheng.Service;
import com.luozheng.Dao.RedisDistributeLock;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import redis.clients.jedis.JedisCluster;

import java.util.UUID;
import java.util.concurrent.TimeUnit;

@Service
public class TicketServiceImpl implements ITicketService{

    private JedisCluster jedisCluster;
    @Autowired
    public void setJedisCluster(JedisCluster jedisCluster) {
        this.jedisCluster = jedisCluster;
    }

    private RedisDistributeLock redislock;

    @Autowired
    public void setLock(RedisDistributeLock redislock) {
        this.redislock = redislock;
    }

    private Logger Logger  = LoggerFactory.getLogger(TicketServiceImpl.class);
    @Override
    public String sellTicket() {
        String ticketStr = jedisCluster.get("ticket");
        int ticket = Integer.valueOf(ticketStr);

        if (ticket>0){
            ticket--;
            jedisCluster.set("ticket",String.valueOf(ticket));
            Logger.info("当前余票："+ticket);
        }else {
            Logger.info("票被抢光了....");
        }
        return "抢票成功...";
    }

    @Override
    public String redisdaoticket() {
        String lockkey = "lockkey";
        /**
         * expiretime：过期时间
         * sleeptime： 重新尝试获取锁等待时间
         * 执行时间
         */
        if (redislock.lock(lockkey, 6,1)){
            int ticket = Integer.valueOf(redislock.get("ticket"));
            if (ticket>0){
                redislock.set("ticket",String.valueOf(--ticket));
                try {
                    Logger.info("执行任务ing....");
                    TimeUnit.SECONDS.sleep(5);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
//                Logger.info("当前余票："+ticket);
            }else {
//                Logger.info("票被抢光了....");
            }
            redislock.unlock(lockkey);
            return "抢票成功...";
        }
        return "被锁住，抢票失败...";
    }
}

```

