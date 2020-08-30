

### 配置

[Mybatis官方文档](https://mybatis.org/mybatis-3/zh/getting-started.html)

#### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <!-- 父工程-->
    <groupId>com.roosevlet</groupId>
    <artifactId>mybatis01</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>mybatis1</module>
        <module>mybatis2</module>
        <module>mybatis3</module>
        <module>mybatis4</module>
        <module>mybatis5</module>
        <module>mybatis6</module>
    </modules>

    <!--导入依赖-->
    <dependencies>
        <!--Mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>
        <!--Mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>
        <!--Junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <!--配置log4j日志-->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.12</version>
        </dependency>
        <!-- lombok方便写实体类 -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
		<!--加载java目录下xml文件  -->
    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <excludes>
                    <exclude>**/*.java</exclude>
                </excludes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.*</include>
                </includes>
            </resource>
        </resources>
  </build>
    <!--解决java版本太高-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
</project>
```

#### mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--引用外部配置环境-->
    <properties resource="db.properties"/>
    <!--此处也可以引入外部文件，但是如果和上面字段相同，则优先使用外部配置文件-->
    <!--配置日志-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
        <!--开启驼峰命名配置-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!--显式开启全局缓存-->
        <setting name="cacheEnabled" value="true"/>
    </settings>

    <!--可以给实体类起别名-->
    <typeAliases>
<!--        <typeAlias type="com.roosevelt.pojo.User" alias="User"/>-->
        <!--扫描实体类，默认别名为类名首字母小写，会使用 Bean 的首字母小写的非限定类名来作为它的别名-->
       <package name="com.roosevelt.pojo"/>
    </typeAliases>

    <!-- 配置 mybatis 的环境 -->
    <environments default="development">
        <!-- 配置 mysql的环境 -->
        <environment id="development">
            <!-- 配置事务的类型 -->
            <transactionManager type="JDBC"/>
            <!-- 配置连接数据库的信息：用的是数据源(连接池) -->
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--每一个Mapper.xml都需要在Mybatis核心文件中注册,如果是注解需要绑定接口-->
    <mappers>
<!--        推荐使用配置文件-->
<!--        <mapper resource="com/roosevelt/dao/UserMapper.xml"/>-->
<!--        如果有很多xml需要配置可以-->
<!--        <mapper resource="com/roosevelt/dao/TeacherMapper.xml"/>-->
<!--        <mapper resource="com/roosevelt/mapper/BlogMapper.xml"/>-->
        <!--利用注解配置就用这种方式-->
<!--        <mapper class="com.roosevelt.dao.UserMapper"/>-->
        <package name="com.roosevelt.dao"/>
    </mappers>

</configuration>
```

#### Mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--绑定对应的Dao/Mapper接口,相当于实现接口-->
<mapper namespace="com.roosevelt.dao.IUserDao">
    <!--查询语句  id 对应接口中方法  resultType返回类型对应泛型
		parameter 参数类型-->
    <select id="getUserList" resultType="com.roosevelt.pojo.User">
    select * from `user`
  </select>
</mapper>
```

#### db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/shop?userSSl=true&amp;useUnicode=true&amp;characterEncoding=UTF-8
username=root
password=root
```

#### log4j配置

```
<settings>
    <setting name="logImpl" value="log4j"/>
</settings>

# Set root category priority to INFO and its only appender to CONSOLE.
#log4j.rootCategory=INFO, CONSOLE            debug   info   warn error fatal
log4j.rootCategory=debug, CONSOLE, LOGFILE

# Set the enterprise logger category to FATAL and its only appender to CONSOLE.
log4j.logger.org.apache.axis.enterprise=FATAL, CONSOLE

# CONSOLE is set to be a ConsoleAppender using a PatternLayout.
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n

# LOGFILE is set to be a File appender using a PatternLayout.
log4j.appender.LOGFILE=org.apache.log4j.FileAppender
log4j.appender.LOGFILE.File=K:\\code\\test.log
log4j.appender.LOGFILE.Append=true
log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.LOGFILE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n
```



### 简介

[Mybatis官网](https://mybatis.org/mybatis-3/zh/getting-started.html)

注意可搜索Github上Mybatis

#### 持久层

* 持久化就是将程序中的数据持久状态和瞬时状态转化过程
* 内存：断电即失
* 数据库，IO文件持久化

#### 优点

- 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件易于学习，易于使用，通过文档和源代码，可以比较完全的掌握它的设计思路和实现。
- 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。 sql写在xml里，便于统一管理和优化。通过sql语句可以满足操作数据库的所有需求。
- 解除sql与程序代码的耦合：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。
- 提供映射标签，支持对象与数据库的orm字段关系映射
- 提供对象关系映射标签，支持对象关系组建维护
- 提供xml标签，支持编写动态sql。 

#### 流程

1. 搭建环境，配置Mybatis依赖以及数据库驱动包，编写MybatisUtils获取sqlsession
2. 编写实体类，创建数据库
3. 编写Mybatis.xml配置文件，这里需要读取数据库信息以及映射Mapper文件
4. 编写util类，首先读取配置文件，用工厂创造sqlsession对象
5. 编写要实现的dao接口，对应实现即mapper.xml
6. mapper.xml要对应dao接口，同时将mapper.xml文件要写入至mybatis配置中
7. 编写测试代码，返回sqlsession对象，然后进行执行

### 第一个Mybatis代码

1. 搭建环境
   * 新建maven项目，导入pom.xml所需要的依赖包
   * 创建module子项目
2. 编写mybatis-config.xml
3. 编写mybatis工具类，读取xml配置文件，返回sqlsession对象，对数据库进行操作

```java
package com.roosevelt.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {
    private  static SqlSessionFactory sqlSessionFactory ;
    static {
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try {
            //获取sqlSessionFactory对象
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            e.printStackTrace();
        }
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    }
    //Sqlsession包含面向数据库执行Sql命令所需的所有方法，
    public static SqlSession getSqlSession(){
        //这里的session其实就是之前jdbc学的connection方法
        return sqlSessionFactory.openSession();
    }
}
```

4. 编写代码

* 编写实体类

```java
package com.roosevelt.pojo;

public class User {
    private int id;
    private String username;
    private String password;

    public User() {
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public User(int id, String username, String password) {
        this.id = id;
        this.username = username;
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

* 编写接口

```java
package com.roosevelt.dao;

import com.roosevelt.pojo.User;

import java.util.List;

public interface IUserDao {
    List<User> getUserList();
}
```

* 编写Mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--绑定对应的Dao/Mapper接口,相当于实现接口-->
<mapper namespace="com.roosevelt.dao.IUserDao">
    <!--查询语句  id 对应接口中方法  返回类型对应泛型-->
    <select id="getUserList" resultType="com.roosevelt.pojo.User">
    select * from `user`
  </select>
</mapper>
```

* 测试代码

```java
package com.roosevelt.Dao;

import com.roosevelt.dao.IUserDao;
import com.roosevelt.pojo.User;
import com.roosevelt.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {
    @Test
    public void test(){
        //1.获得sqlsession对象
        SqlSession sqlSession = null;
        try {
            sqlSession = MybatisUtils.getSqlSession();
            //方式一.执行sql
            IUserDao mapper = sqlSession.getMapper(IUserDao.class);
            List<User> userList = mapper.getUserList();
            for (User user : userList) {
                System.out.println(user);
            }
            //方式二：不推荐使用
            System.out.println("----------");
            List<User> userList2 = sqlSession.selectList("com.roosevelt.dao.IUserDao.getUserList");
            for (User user : userList2) {
                System.out.println(user);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
          //确保sqlSession关闭
            if (sqlSession!=null){
                sqlSession.close();
            }
        }
      
    }
}
```

#### 常见错误信息

* org.apache.ibatis.binding.BindingException: Type interface com.roosevelt.dao.IUserDao is not known to the MapperRegistry. 		未在mybatis-config中配置mapper映射

### CRUD

* 问题：为什么执行插入操作的时候，一条语句执行两次

* 注意：修改删除
* 要提交事务

#### IUserDao

```java
package com.roosevelt.dao;

import com.roosevelt.pojo.User;

import java.util.List;

public interface IUserDao {
    /**
     * 查询全部用户
     * @return
     */
    List<User> getUserList();
    /**
     * 根据id查询用户
     * @param i
     */
    User getUserById(int i);
    /**
     * 插入一个用户
     */
    void addUser(User user);
    /**
     * 修改用户
     */
    int updateUser(User user);
    /**
     * 删除用户
     * @param user
     */
    int deleteUser(int user);
}
```

#### UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--绑定对应的Dao/Mapper接口,相当于实现接口-->
<mapper namespace="com.roosevelt.dao.IUserDao">
    <!--查询语句  id 对应接口中方法  返回类型对应泛型-->
    <select id="getUserList" resultType="com.roosevelt.pojo.User">
    select * from `user`
  </select>

    <select id="getUserById" resultType="com.roosevelt.pojo.User" parameterType="int">
        select * from user where id = #{id}
    </select>
    <!--注意此处要username和实体类要对应一一对应-->
    <insert id="addUser" parameterType="com.roosevelt.pojo.User">
        insert into user values(#{id},#{username},#{password})
    </insert>
    <update id="updateUser" parameterType="com.roosevelt.pojo.User">
        update user set username = #{username},password = #{password} where id =#{id}
    </update>
    <!--参数类型为int可以不用写，返回类型为int也可以不用写-->
    <delete id="deleteUser" parameterType="int" >
        delete from user where id = #{id}
    </delete>
</mapper>
```

#### 测试

```java
package com.roosevelt.Dao;

import com.roosevelt.dao.IUserDao;
import com.roosevelt.pojo.User;
import com.roosevelt.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Ignore;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {
    private final SqlSession sqlSession = MybatisUtils.getSqlSession();
   private final IUserDao mapper = sqlSession.getMapper(IUserDao.class);
    @Ignore
    public void test(){
        //1.获得sqlsession对象
        try {
            //方式一.执行sql
            List<User> userList = mapper.getUserList();
            for (User user : userList) {
                System.out.println(user);
            }
            //方式二：不推荐使用
            System.out.println("----------");
            List<User> userList2 = sqlSession.selectList("com.roosevelt.dao.IUserDao.getUserList");
            for (User user : userList2) {
                System.out.println(user);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (sqlSession!=null){
                sqlSession.close();
            }
        }
    }
    @Ignore
    public void getQueryById(){
        User user = mapper.getUserById(1);
        System.out.println(user);
        sqlSession.close();
    }

    /**
     * 增删改要提交事务
     */
    @Ignore
    public void addUser(){
        User user = new User();
        user.setId(9);
        user.setPassword("123456");
        user.setUsername("nihao");
        mapper.addUser(user);
        sqlSession.commit();
        sqlSession.close();
    }
    @Ignore
    public void updateUser(){
        User user = new User(3, "lz", "123");
        int i = mapper.updateUser(user);
        System.out.println(i);
        sqlSession.commit();
        sqlSession.close();
    }
    @Test
    public void deleteUser(){
        int i = mapper.deleteUser(1);
        System.out.println(i);
        sqlSession.commit();
        sqlSession.close();
    }
}
```

#### 万能Map

实体类或数据库表中字段或参数过多，应当考虑使用Map

```
int  addUser(Map<String,Object> map);

  <insert id="addUser" parameterType="map">
        insert into user values(#{userid},#{username},#{password})
    </  <insert id="addUser" parameterType="map">
        insert into user values(#{userid},#{username},#{password})
    </insert>>
```

* Map传递参数，直接在sql中取出key即可
* 对象传递参数，直接在sql中取对象的属性即可
* 只有一个基本类型参数的情况下，直接在sql中取到
* 多个参数用Map，或者注解

#### 模糊查询

1. java代码直接 `select * from user where name like #{value}     map.getUserlike("%李%")`
2. 在sql拼接中使用通配符,可能产生sql注入

select * from user where name like "%"#{value}"%"

### 配置解析

#### 环境配置environments

* Mybatis可以配置多个环境，每个sqlSessionFactory实例只能选择一种环境

```xml
<environments default="development">
    <!-- 配置 mysql的环境 -->
    <environment id="development">
    
    <!-- 配置 test环境 -->
    <environment id="test">  
```

* Mybatis默认事务管理器JDBC(还有managed)，池子为：pooled（还有unpooled,jndi）

#### 属性properties

* 编写配置文件

```xml
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/shop?userSSl=true&amp;useUnicode=true&amp;characterEncoding=UTF-8
username=root
password=root
```

* 引入配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--引用外部配置环境-->
    <properties resource="db.properties"/>
		<!--此处也可以引入外部文件，但是如果和上面字段相同，则优先使用外部配置文件-->

    <!-- 配置 mybatis 的环境 -->
    <environments default="development">
        <!-- 配置 mysql的环境 -->
        <environment id="development">
            <!-- 配置事务的类型 -->
            <transactionManager type="JDBC"/>
            <!-- 配置连接数据库的信息：用的是数据源(连接池) -->
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--每一个Mapper.xml都需要在Mybatis核心文件中注册-->
    <mappers>
        <mapper resource="com/roosevelt/dao/UserMapper.xml"/>
<!--        <mapper class="com.roosevelt.Dao.IUserDao"/>-->
    </mappers>

</configuration>
```

#### 类型别名（typeAliases）

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写

```
<!--可以给实体类起别名-->
<typeAliases>
    <typeAlias type="com.roosevelt.pojo.User" alias="User"/>
</typeAliases>
```

也可以指定包名，

```xml
 <!--可以给实体类起别名-->
    <typeAliases>
<!--        <typeAlias type="com.roosevelt.pojo.User" alias="User"/>-->
        <!--扫描实体类，默认别名为类名首字母小写，会使用 Bean 的首字母小写的非限定类名来作为它的别名-->
       <package name="com.roosevelt.pojo"/>
    </typeAliases>
```

在实体类比较少的时候，建议第一种

实体类十分多建议第二种

第一种可以diy别名，第二种不行，是在非要改，可以使用别名

也可以在实体类上使用注解，注解优先级较高

```java
@Alias("hello")
public class User {
```

#### 设置（settings）

* 开启缓存
* 懒加载
* 驼峰命名

#### 其他配置

- [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)

- [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)

- [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)
  
- mybatisplus
  
- 映射器：作用注册绑定Mapper文件

  * 方式一：利用resouce绑定

    ```
    <mappers>
            <mapper resource="com/roosevelt/dao/UserMapper.xml"/>
        </mappers>
    ```

  * 方式二：使用class绑定

    ```
    <mappers>
            <mapper class="com.roosevelt.Dao.IUserDao"/>
        </mappers>
    ```

    注意点：接口和Mapper文件必须同名同目录

  * 方式三：使用package进行注入绑定

  ```
   <mappers>
          <package name="com.roosevelt.dao"/>
   </mappers>
  ```

  ​			注意点：接口和Mapper文件必须同名同目录

#### 生命周期和作用域

开始--sqlsessionfactorybuilder（加载mybatis-config.xml配置文件）--sqlsessionfacotry--sqlsession--mapper--执行sql--结束

* sqlsessionfactorybuilder:
  * 一旦创建，就不再需要
  * 局部变量
* sqlsessionfactory
  * 理解为数据库连接池
  * 一旦创建，运行期间一直存在
  * 全局作用域，程序开始就开始，程序结束在结束
  * 单例模式或静态单例模式
* sqlsession
  * 连接池的一个请求
  * 不是线程安全的，因此不能被共享
  * 用完之后需要关闭
* mapper
  * 每一个mapper代表一个具体的业务

### ResultMap

* 如果数据库名和实体类名不同
  * 起别名

    ```
    <select id="getUserById" resultType="user" parameterType="int">
        select id,username,password as pwd from user where id = #{id}
    </select>
    ```

* resultMap

#### 结果集映射

```xml
<!--结果集映射-->
<resultMap id="userMap" type="User">
    <!--conlumn数据库字段，property实体类中属性-->
    <result column="password" property="pwd"/>
</resultMap>

<select id="getUserById" resultMap="userMap" parameterType="int">
    select * from user where id = #{id}
</select>
```

* `resultMap` 元素是 MyBatis 中最重要最强大的元素

* 对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了

### 日志

#### 日志工厂

在Mybatis中具体使用哪个日志，在设置中

| logImpl | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。 | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING |
| ------- | ----------------------------------------------------- | ------------------------------------------------------------ |
|         |                                                       |                                                              |

* log4j

1. 设置

```
<!--配置日志-->
<settings>
    <setting name="logImpl" value="log4j"/>
</settings>
```

2. 配置log4j.properties

```
# Set root category priority to INFO and its only appender to CONSOLE.
#log4j.rootCategory=INFO, CONSOLE            debug   info   warn error fatal
log4j.rootCategory=debug, CONSOLE, LOGFILE

# Set the enterprise logger category to FATAL and its only appender to CONSOLE.
log4j.logger.org.apache.axis.enterprise=FATAL, CONSOLE

# CONSOLE is set to be a ConsoleAppender using a PatternLayout.
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n

# LOGFILE is set to be a File appender using a PatternLayout.
log4j.appender.LOGFILE=org.apache.log4j.FileAppender
log4j.appender.LOGFILE.File=K:\\code\\test.log
log4j.appender.LOGFILE.Append=true
log4j.appender.LOGFILE.layout=org.apache.log4j.PatternLayout
log4j.appender.LOGFILE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n
```

* stdout_logging

```
<!--配置日志,不需要-->
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

### 分页

> 减少数据处理量，使用limit分页

* limit分页：起始位置，页面大小

`select * from account limit 0,3`

* 接口

```java
List<User> getUserByLimit(Map<String,Integer> map);
```

```xml
<select id="getUserByLimit" resultType="user" parameterType="map">
    select * from user limit #{startIndex},#{pageSize}
</select>
```

```java
@Test
public void getUserByLimit(){
    HashMap<String, Integer> map = new HashMap<>();
    map.put("startIndex",1);
    map.put("pageSize",3);
    List<User> users = mapper.getUserByLimit(map);
    for (User user : users) {
        System.out.println(user);
    }
    sqlSession.close();
}
```

* Rowbounds分页

```
List<User> getUserByRowBounds();
```

```xml
<select id="getUserByRowBounds" resultType="user" >
    select * from user
</select>
```

```java
@Test
public void getUserByRowbounds(){
    RowBounds rowBounds = new RowBounds(1, 3);
    List<User> users = sqlSession.selectList("com.roosevelt.dao.IUserDao.getUserByRowBounds",null,rowBounds);
    for (User user : users) {
        System.out.println(user);
    }
    sqlSession.close();
}
```

* [分页插件](https://gitee.com/free/Mybatis_PageHelper)

### 注解开发

```
@Select("select * from user")
List<User> getUserList();
```

```
    <!--每一个Mapper.xml都需要在Mybatis核心文件中注册,如果是注解需要绑定接口-->
    <mappers>
<!--        <mapper resource="com/roosevelt/dao/UserMapper.xml"/>-->
        <mapper class="com.roosevelt.dao.IUserDao"/>
<!--        <package name="com.roosevelt.dao"/>-->
    </mappers>
```

* 底层主要应用反射
* `IUserDao mapper = sqlSession.getMapper(IUserDao.class);`获取类
* 通过上类推导返回类型之类

#### Mybatis执行流程

https://baijiahao.baidu.com/s?id=1657345168237187261&wfr=spider&for=pc

1. 获取mybatis.xml解析成一个流
2. sqlsessionfacotorybuild.build(inpu)

#### CRUD

设置自动提交事务

注意要注册

```
//Sqlsession包含面向数据库执行Sql命令所需的所有方法，
public static SqlSession getSqlSession(){
    //这里的session其实就是之前jdbc学的connection方法
    //这里设置为true，则开启事务
    return sqlSessionFactory.openSession(true);
}
```

```JAVA
package com.roosevelt.dao;

import com.roosevelt.pojo.User;
import org.apache.ibatis.annotations.*;

import java.util.List;
import java.util.Map;

public interface IUserDao {
    /**
     * 查询全部用户
     * @return
     */
    @Select("select * from user")
    List<User> getUserList();
    /**
     * 根据id查询用户
     * @param id
     */
    //方法存在多个参数所有参数前面加@Param,只有一个可以不用写
    @Select("select * from user where id = #{id}")
    User getUserById( int id);
    /**
     * 插入一个用户
     */
    @Insert("insert into user values(#{id},#{username},#{password})")
    int addUser(User user);
    /**
     * 修改用户
     */
    @Update("update user set username =#{username},password = #{password} where id = #{id}")
    int updateUser(User user);
    /**
     * 删除用户
     * @param id
     */
    @Delete("delete from user where id =#{id}")
    int deleteUser(int id);
}
```

#### @Param()

* 基本类型或者String类型，需要加上
* 引用类型不需要加
* 只有一个基本类型可以省略，建议加上
* sql中引用的就是这里的@Param("")中设定的属性

#### #()和$() 

$()符号相当于statement，不安全，sql注入隐患

#### Lombok

1. 安装lobbok

   1. plugins搜索lombok
   2. 导入lombok jar包

   ```
   <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
       <version>1.18.8</version>
       <scope>provided</scope>
   </dependency>
   ```

   ```
   @Getter and @Setter
   @FieldNameConstants
   @ToString
   @EqualsAndHashCode
   有参构造
   @AllArgsConstructor, @RequiredArgsConstructor and @NoArgsConstructor
   @Log, @Log4j, @Log4j2, @Slf4j, @XSlf4j, @CommonsLog, @JBossLog, @Flogger, @CustomLog
   @Data   ：无参构造，get，set，tostring，hashcode
   @Builder
   @SuperBuilder
   @Singular
   @Delegate
   @Value
   @Accessors
   @Wither
   @With
   @SneakyThrows
   @val
   @var
   experimental @var
   @UtilityClass
   Lombok config system
   ```

### 集合和关联

#### 一（多）对一

##### 嵌套查询

1. 先查询一个
2. 返回类型继续写
3. 类型一一对应，将关联的进行子查询，注意写column
4. 如果id是主键可以<id property="id">

```xml
  <select id="getStudent" resultMap="StudentTeacher">
        select * from student;
    </select>
    <resultMap id="StudentTeacher" type="Student">
        <result property="id" column="id"/>
        <result property="name" column="name"/>
<!--        复杂的属性单独处理
               对象：association
               集合：collection-->
        <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
    </resultMap>
    <select id="getTeacher" resultType="Teacher">
        select * from teacher where id =#{id};
    </select>
```

##### 结果查询

1. 先确定要查询的内容，一次性查找出来
2. 写一个resultMap
3. 分别对应结果集列
4. 如果存在对象，则先association  将javaType实体类 与属性类名对上
5. 利用result 将对象属性意义队形

```sql
<select id="getStudent2" resultMap="StudentTeacher2">
    select student.id,student.name,teacher.name tname,teacher.id tid from student,teacher where student.id = teacher.id
</select>
<resultMap id="StudentTeacher2" type="Student">
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <association property="teacher" javaType="Teacher">
        <result property="name" column="tname"/>
        <result property="id" column="tid"/>
    </association>
</resultMap>
```

#### 一对多查询

##### 嵌套查询 （有些可以省略）

```sql
<!--嵌套查询-->
<!--查询老师，封装结果-->
<select id="getTeacher" resultMap="teacherStudent">
    select * from teacher where id = #{id}
</select>
<!--结果封装-->
<resultMap id="teacherStudent" type="Teacher">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <!--需要实体类名称，列名称，类型，泛型信息，以及通过哪个查-->
    <collection property="students" column="id" javaType="List" ofType="Student" select="getStudent">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
    </collection>
</resultMap>
<!--查询学生，封装结果-->t
<select id="getStudent" resultType="Student">
    select * from student where tid =#{id}
</select>
```

##### 结果查询  建议使用

查五个信息要一一对应，不可忽略

```sql
<!--结果查询-->
<!--编写查询信息-->
    <select id="getTeacher2" resultMap="teacherStudent2">
        SELECT teacher.*,student.id sid,student.`name` sname
        FROM teacher,student
        WHERE teacher.id = student.tid and teacher.id = #{id};
    </select>
    <resultMap id="teacherStudent2" type="Teacher">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <!--注意javatype只适合于多对一，类似集合中的泛型信息，而集合要用ofType-->
        <collection property="students" ofType="Student">
            <id property="id" column="sid"/>
            <result property="name" column="sname"/>
            <result property="tid" column="tid"/>
        </collection>
    </resultMap>
```

#### 对比

* javaType指实体类属性类型 student
* oftype指定容器类型
* 嵌套查询要写javatype，结果查询可以不用
* assiociation多对一
* collection一对多

### 动态Sql

> 根据不同的条件生成不同的sql语句

#### if/where

* 常用于select

*where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除。

```
List<Blog> queryBlog(Map map);
```

```xml
<select id="queryBlog" parameterType="map" resultType="Blog">
    select * from blog where 1=1
    <if test="title!=null">
        and title =#{title}
    </if>
    <if test="author!=null">
        and title =#{author}
    </if>
</select>

<select id="queryBlog" parameterType="map" resultType="Blog">
        select * from blog 
        <where>
            <if test="title!=null">
                and title =#{title}
            </if>
            <if test="author!=null">
                and title =#{author}
            </if>
        </where>
        
    </select>
```

#### choose

* 常用于select

只会选择一个，如果都不满足，则走otherwise

```xml
<select id="queryBlogChoose" parameterType="map" resultType="Blog">
select * from blog
<where>
    <choose>
        <when test="title!=null">title =#{title}</when>
        <when test="author!=null">and author =#{author}</when>
        <otherwise>and views =#{views}</otherwise>
    </choose>
</where>
</select>
```

```java
@Test
    public void queryblog(){
        HashMap<String,Object> map= new HashMap<>();
        map.put("title","title1");
        map.put("views",0);
        List<Blog> blogs = mapper.queryBlogChoose(map);
        for (Blog blog : blogs) {
            System.out.println(blog);
        }
    }
```

#### set

* 用于update

```xml
<update id="updateBlog" parameterType="map">
        update blog
        <set>
            <if test="title!=null">
                title = #{title},
            </if>
            <if test="author!=null">
                author = #{author}
            </if>
        </set>
        where id =#{id}
</update>
```

#### foreach

* 用于select

```xml
List<Blog> queryBlogForeach(Map map);
```

```xml
<!--    select * from blog where 1=1 and (id =1 or id =2)-->
    <select id="queryBlogForeach" parameterType="map" resultType="blog">
        select * from blog
        <where>
            <foreach collection="ids" item="id" open="(" close=")" separator="or">
                id = #{id}
            </foreach>
        </where>
    </select>
```

```xml
    @Test
    public void queryblogforeach(){
        HashMap<String,Object> map =new HashMap<>() ;
        ArrayList<Integer> ids = new ArrayList<>();
//        不添加则select * from blog
        ids.add(1);
        ids.add(2);
        map.put("ids",ids);
        List<Blog> blogs = mapper.queryBlogForeach(map);
        for (Blog blog : blogs) {
            System.out.println(blog);
        }
        sqlSession.close();
    }
```

#### sql片段

1. sql抽取公共的部分
2. 在需要使用的地方使用include标签

```xml
<sql id="if-title-author">
    <if test="title!=null">
        and title =#{title}
    </if>
    <if test="author!=null">
        and author=#{author}
    </if>
</sql>
<select id="queryBlog" parameterType="map" resultType="Blog">
    select * from blog
    <where>
        <include refid="if-title-author"></include>
    </where>
</select>
```

* 最好基于单表定义sql片段
* 不要存在where标签



### 缓存

> 查询需要连接数据库耗资源
>
> 一次查询结果，可暂存在直接取到的地方  -->内存：缓存
>
> 再次查询相同数据走缓存，不用走数据库

* 经常查询且不经常改变的数据使用缓存
* 为什么使用缓存，减少数据库交互次数，减少系统开销，提高系统效率

#### 一级缓存

> 默认　Mybatis对缓存提供支持，但是在没有配置的默认情况下，它只开启一级缓存，一级缓存只是相对于同一个SqlSession而言。所以在参数和SQL完全一样的情况下，我们使用同一个SqlSession对象调用一个Mapper方法，往往只执行一次SQL，因为使用SelSession第一次查询后，MyBatis会将其放在缓存中，以后再查询的时候，如果没有声明需要刷新，并且缓存没有超时的情况下，SqlSession都会取出当前缓存的数据，而不会再次发送SQL到数据库。

```java
@Test
public void queryById(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.queryUserById(15);
    System.out.println(user);
    System.out.println("=============");
    User user1 = mapper.queryUserById(15);
    System.out.println(user==user1);
    sqlSession.close();
}

true

增加一条增删改语句
System.out.println("+++++");
User user2 = new User(4, "cache", "password");
mapper.updateUser(user2);

  sqlSession.clearCache();
```

缓存失效的情况

* 查询不同的东西
* 增删改操作，可能改变原有的数据，必定刷新缓存
* 查询不同的Mapper.xml
* 手动清理缓存sqlSession.clearCache();

#### 二级缓存

> 基于namespace级别的缓存，一个名称空间，对应一个二级缓存
>
> 工作机制
>
> ​	一个会话查询一条语句，这个数据就会被放在当前会话的一级缓存中
>
> ​	如果会话关闭，一级缓存数据中被保存到二级缓存中
>
> ​	新的会话查询信息，可以从二级缓存中获取
>
> ​	不同的mapper查询数据放在对应的缓存map中

```
显示的在mabatis配置文件中开启全局缓存，默认开启的
<settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
        <!--开启驼峰命名配置-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!--显式开启全局缓存-->
        <setting name="cacheEnabled" value="true"/>
    </settings>
在Mapper.xml中配置二级缓存
<cache/>

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.roosevelt.dao.UserMapper">
    <cache/>
<select id="getUsers" resultType="User">
    select * from user
</select>
</mapper>
```

```java
@Test
public void queryById(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    SqlSession sqlSession2 = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    UserMapper mapper2 = sqlSession2.getMapper(UserMapper.class);
    User user = mapper.queryUserById(15);
    User user1 = mapper2.queryUserById(15);
    System.out.println(user==user1);
    sqlSession.close();
    sqlSession2.close();
}

false
```

先将sqlsession关闭，再从二级缓存中查找

```java
@Test
public void queryById(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    SqlSession sqlSession2 = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    User user = mapper.queryUserById(15);
    sqlSession.close();
    UserMapper mapper2 = sqlSession2.getMapper(UserMapper.class);
    User user1 = mapper2.queryUserById(15);
    System.out.println(user==user1);
    sqlSession2.close();
}
```

* 基于注解的缓存策略？
* 对于二级缓存取出来的对象不相等？
* 实体类最好实现序列化
* 只要开启勒二级缓存，在同一个Mapper就有效
* 所有的数据都会先放在一级缓存中，只有会话提交或关闭才会关闭二级缓存中

#### 缓存原理

![image-20200530183830707](https://gitee.com/shaobing2021/typora/raw/master/img/20200728100025.png)

#### 自定义缓存

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.2.1</version>
</dependency>

```

```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```

ehcache.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
        updateCheck="false">
   <!--
      diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
      user.home – 用户主目录
      user.dir – 用户当前工作目录
      java.io.tmpdir – 默认临时文件路径
    -->
   <diskStore path="./tmpdir/Tmp_EhCache"/>
   
   <defaultCache
           eternal="false"
           maxElementsInMemory="10000"
           overflowToDisk="false"
           diskPersistent="false"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="259200"
           memoryStoreEvictionPolicy="LRU"/>

   <cache
           name="cloud_user"
           eternal="false"
           maxElementsInMemory="5000"
           overflowToDisk="false"
           diskPersistent="false"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="1800"
           memoryStoreEvictionPolicy="LRU"/>
   <!--
      defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
    -->
   <!--
     name:缓存名称。
     maxElementsInMemory:缓存最大数目
     maxElementsOnDisk：硬盘最大缓存个数。
     eternal:对象是否永久有效，一但设置了，timeout将不起作用。
     overflowToDisk:是否保存到磁盘，当系统当机时
     timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
     timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
     diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
     diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
     diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
     memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
     clearOnFlush：内存数量最大时是否清除。
     memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
     FIFO，first in first out，这个是大家最熟的，先进先出。
     LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
     LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
  -->

</ehcache>
```