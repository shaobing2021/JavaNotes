## 整合RabbitMq2

[路由方式](https://www.rabbitmq.com/getstarted.html)

### 依赖

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.shoabing</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

### 配置文件

```yml
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=root
spring.rabbitmq.password=root
spring.rabbitmq.virtual-host=/root
```

### 模型一Hello World

* 队列

![img](https://gitee.com/shaobing2021/typora/raw/master/img/20200819141923.png)

> 消费者

```java
package com.shaobing.hello;

import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Component
//代表是一个监听者，没有队列，声明一个队列，默认持久化 非独占(独占影响效率) 非自动删除队列
@RabbitListener(queuesToDeclare = @Queue(value = "hello",durable = "true",autoDelete = "true"))
public class HelloCustomer {
    /**
     * 代表回调方法
     * @param message
     */
    @RabbitHandler
    public void receive(String message){
        System.out.println(message);
    }
}
```

> 生产者测试类

```java
package com.shaobing;

import org.junit.jupiter.api.Test;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class RabbitmqApplicationTests {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    @Test
    void contextLoads() {
        rabbitTemplate.convertAndSend("hello","helloworld");
    }

}
```

### 模型二Work queues

![img](https://gitee.com/shaobing2021/typora/raw/master/img/20200819141950.png)

* 工作队类
* 公平调度，可以实现不公平，如果这里只有一个生产者就只会有一个消费者消费
* 注意可能出现问题，则要把队列删了

> 消费者

```java
package com.shaobing.hello;

import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Component
public class WorkCustomer {
    @RabbitListener(queuesToDeclare = @Queue("work"))
    public void receive1(String message){
        System.out.println("message1:"+message);
    }
    @RabbitListener(queuesToDeclare = @Queue("work"))
    public void receive2(String message){
        System.out.println("message2:"+message);
    }
}
```

> 生产者测试类

```java
package com.shaobing;

import org.junit.jupiter.api.Test;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class RabbitmqApplicationTests {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    @Test
    void contextLoads() {
        rabbitTemplate.convertAndSend("hello","helloworld");
    }
    @Test
    void testwork() {
        for (int i = 0; i < 10; i++) {
            rabbitTemplate.convertAndSend("work","hello work"+i);
        }
    }
}
```

### 模型三Fanout

* 测试广播方式
* 发布订阅

![](https://gitee.com/shaobing2021/typora/raw/master/img/20200819142056.png)

> 消费者

```java
package com.shaobing.hello;

import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;
//广播消费
@Component
public class FanoutCustomer {
    @RabbitListener(bindings = {
            @QueueBinding(value = @Queue,//创建临时队列
            exchange = @Exchange(value = "logs",type = "fanout"))
    })
    public void receive1(String message){
        System.out.println("message1:"+message);
    }
    @RabbitListener(bindings = {
            @QueueBinding(value = @Queue,
                    exchange = @Exchange(value = "logs",type = "fanout"))
    })
    public void receive2(String message){
        System.out.println("message2:"+message);
    }
}
```

> 测试以及生产者

* 两个消费者均会输出

```java
@Test
void testFanout() {
    rabbitTemplate.convertAndSend("logs","","hello fanout");
}
```

#### 配置类

> 绑定交换机和队列

```java
package com.shaobing.config;

import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.FanoutExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;
//注入到spring容器中
@Component
public class FanoutConfig {
    //定义邮件队列
    private String FANOUT_EMAIL_QUEUE = "emailQueue";
    //定义交换机名称
    private String EXCHANGE_NAME = "fanoutExchange";
    //1.定义邮件队列
    @Bean
    public Queue fanoutEmailQueue(){
        return new Queue(FANOUT_EMAIL_QUEUE);
    }
    //2.定义交换机
    @Bean
   public FanoutExchange fanoutExchange(){
        return new FanoutExchange(EXCHANGE_NAME);
    }
    //3.交换机和队列绑定 参数名称和定义队列和交换机方法名称一致
    @Bean
    public Binding bindExchangeEmail(Queue fanoutEmailQueue, FanoutExchange fanoutExchange){
        return BindingBuilder.bind(fanoutEmailQueue).to(fanoutExchange);
    }
}
```

> 发送对象

```java
package com.shaobing.email;

import com.fasterxml.jackson.annotation.JsonFormat;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.shaobing.pojo.User;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class FanoutEmailProducer {

    @Autowired
    private RabbitTemplate rabbitTemplate;
    public void send(String queueName){
        User user = new User();
        user.setPassword("12345");
        user.setUsername("hello");
        System.out.println("queueName:" +queueName);
        //发送消息
        ObjectMapper mapper = new ObjectMapper();
        String s = null;
        try {
            s = mapper.writeValueAsString(user);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        rabbitTemplate.convertAndSend(queueName,s);
    }
}
```

> 消费者

```java
package com.shaobing.mq.com.shaobing;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;
@Component
public class EmailConsumer {
    @RabbitListener(queues = "emailQueue")
    public void email(String user){
        ObjectMapper mapper = new ObjectMapper();
        User u = null;
        try {
            u = mapper.readValue(user, User.class);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        System.out.println("email：" +u);
    }
}
```

### 模型四route

![img](https://gitee.com/shaobing2021/typora/raw/master/img/20200819142113.png)

> 消费者

```java
package com.shaobing.hello;

import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Component
public class RouteCutomer {
    @RabbitListener(bindings ={
            @QueueBinding(
                    value = @Queue,//创建临时队列
                    //默认type="direct"，因而可以省略,指定交换机名称和类型
                    exchange = @Exchange("directs"),
                    key={"info","error","warn"}//和生产者参数绑定
            )
    })
    public void receive1(String message){
        System.out.println("message1:"+message);
    }
    @RabbitListener(bindings ={
            @QueueBinding(
                    value = @Queue,//创建临时队列
                    //默认type="direct"，因而可以省略,指定交换机名称和类型
                    exchange = @Exchange("directs"),
                    key={"error"}
            )
    })
    public void receive2(String message){
        System.out.println("message2:"+message);
    }
}
```

> 测试类和消费者

```java
@Test //路由模式
void testRoute() {
    rabbitTemplate.convertAndSend("directs","error","hello directs info");
}
```

### 路由方式五Topics

![img](https://gitee.com/shaobing2021/typora/raw/master/img/20200819142219.png)

* 相比route模式控制更细粒度

> 消费者

```java
package com.shaobing.hello;

import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Component
public class TopicCustomer {
    @RabbitListener(bindings = {
            @QueueBinding(value = @Queue,
            exchange = @Exchange(type = "topic",name = "topics"),
            key = {"user.save","user.*"})
    })
    public void receive1(String message){
        System.out.println("message1："+message);
    }
    @RabbitListener(bindings = {
            @QueueBinding(value = @Queue,
                    exchange = @Exchange(type = "topic",name = "topics"),
                    key = {"user.save","order.*"})
    })
    public void receive2(String message){
        System.out.println("message2："+message);
    }
}
```

> 生产者和测试类

* 只有receive2能够接收

```java
@Test
void testTopic(){
    rabbitTemplate.convertAndSend("topics","order.save","user.save key ");
}
```

### [死信队列](https://www.rabbitmq.com/dlx.html)

> 概述

![](https://oscimg.oschina.net/oscnet/3e1b0f76280f94d5fb3ca142d89063de368.jpg)

* 根据死信交换机和死信路由==>私信队列
* 