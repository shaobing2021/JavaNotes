### 配置

#### 数据库

#### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.roosevelt</groupId>
    <artifactId>ssmbuild</artifactId>
    <version>1.0-SNAPSHOT</version>
    <dependencies>
<!--junit,数据库驱动,连接池（c3p0），mybatis-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.6</version>
        </dependency>
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.5</version>
        </dependency>
        <!--spring-mybaits，spring，jdbc    -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.4</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        <!--    servelt，jsp，jstl-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
<!--        lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
            <scope>provided</scope>
        </dependency>
        <!--如果需要使用aop，则要导入该aop织入包-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.13</version>
        </dependency>
    </dependencies>
<!--    静态资源导出问题，防止资源文件被过滤了-->
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
<!--    针对jdk版本问题-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
</project>
```

#### mybatis-config.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--引用外部配置环境-->
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

#### applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--  导入spring关联mybatis的配置-->
    <import resource="spring-dao.xml"/>
    <import resource="spring-mvc.xml"/>
    <import resource="spring-service.xml"/>
    <!--    <import resource="spring-mvc.xml"/>   -->
</beans>
```

#### db.properties

```xml
driver=com.mysql.jdbc.Driver
#Mysql 8.0 增加时区配置 serverTimezone=Asia/Shanghai
url=jdbc:mysql://localhost:3306/ssmbuild?userSSl=true&useUnicode=true&characterEncoding=utf8
user=root
password=root
```

#### BookMapper.xml

在dao目录下

```xml
package com.roosevelt.dao;

import com.roosevelt.pojo.Book;
import org.apache.ibatis.annotations.Param;

import java.util.List;

public interface BookMapper {
    int insertBook(Book book);
    int deleteBookById(@Param("bookID") int id);
    int updateBook(Book book);
    Book queryBook(@Param("bookID") int id);
    List<Book> queryAllBooks();
    List<Book> queryBookByName(@Param("bookName")String bookName);
}
```

#### spring-dao.xml

IOC注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd">
<!--1.关联数据库配置文件-->
   <context:property-placeholder location="classpath:db.properties"/>
<!--2.配置数据库连接池
    dbcp：半自动化操作，不能自动连接
    c3p0：自动化操作，自动加载配置文件，并且可以自动设置到对象中
    druid：
    hikari-->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="driverClass" value="${driver}"/>
    <property name="jdbcUrl" value="${url}"/>
    <property name="user" value="${user}"/>
    <property name="password" value="${password}"/>

    <!-- c3p0连接池的私有属性 -->
    <property name="maxPoolSize" value="30"/>
    <property name="minPoolSize" value="10"/>
    <!-- 关闭连接后不自动commit -->
    <property name="autoCommitOnClose" value="false"/>
    <!-- 获取连接超时时间 -->
    <property name="checkoutTimeout" value="10000"/>
    <!-- 当获取连接失败重试次数 -->
    <property name="acquireRetryAttempts" value="2"/>
</bean>

    <!--    3.配置sqlsessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--       绑定数据源-->
        <property name="dataSource" ref="dataSource" />
        <!--        绑定mabatis配置文件-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>
<!--    4.配置dao接口扫描包，动态实现Dao接口注入到spring容器中-->
   <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
       <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
<!--   要扫描的dao包    -->
       <property name="basePackage" value="com.roosevelt.dao"/>
   </bean>
</beans>
```

#### spring-service.xml

AOP切面

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd">
<!--   1. 扫描service下的包-->
    <context:component-scan base-package="com.roosevelt.service"/>
<!--   2. 将所有业务类注入到spring，通过配置或者注解即service和autowired -->
    <bean id="BookServiceImpl" class="com.roosevelt.service.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>
<!--    3.声明式配置-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
<!--        注入数据源-->
        <property name="dataSource" ref="dataSource"/>
    </bean>
<!--    4.aop事务配置-->
<!--    开启声明式事务-->
    <!--    结合aop实现事务织入-->
    <!--    配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--        给哪些方法配置事务-->
        <!--        配置事务的传播特性 propagation七种事务 默认required
        -->
        <tx:attributes>
            <tx:method name="add" propagation="REQUIRED"/>
            <tx:method name="delete"/>
            <tx:method name="update"/>
            <tx:method name="query" read-only="true"/>
            <tx:method name="*"/>
        </tx:attributes>
    </tx:advice>
    <!--    配置aop-->
    <aop:config>
        <aop:pointcut id="pointcut" expression="execution(* com.roosevelt.dao.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```

#### web.xml

add framework surpport

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--    配置DispatcherServlet，spring核心，请求分发器，前端控制器-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
<!--        这里作用???-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <!--        起动级别1，服务器启动，他就启动-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <!--    在springmvc中
    /：不会匹配jsp页面
    /*：匹配所有请求，包括jsp-->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!--解决数据乱码问题，通常使用过滤器，保证浏览器和控制台数据一致性-->
    <filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>
</web-app>
```

#### spring-mvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd">

    <!--1.处理器映射适配器,支持mvc注解驱动，使@RequestMapping生效
       自动注入映射器和适配器实例-->
    <!--<mvc:annotation-driven/>-->

    <!--2.静态资源过滤
      .css,.js,.html,.mp3,.mp4-->
    <!--<mvc:default-servlet-handler/>-->

    <!--3.自动扫描包，让该包下的注解生效，由IOC统一
    管理-->
    <context:component-scan
            base-package="com.roosevelt.controller"/>


    <!--4.视图解析器：模板引擎Thymeleaf，Freemarker-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!--        前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--        后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
    <!--    <bean id="/hello" class="com.roosevelt.controller.HelloController"/>-->
</beans>
```

### CRUD

#### 重定向和转发

1. forward: 访问 Servlet 处理业务逻辑，然后 forward 到 jsp 显示处理结果，浏览器里 URL 不变

2. redirect: 提交表单，处理成功后 redirect 到另一个 jsp，防止表单重复提交，浏览器里 URL 变了

#### 1.编写首页

>  简单的jsp页面，进入查询控制器逻辑

```html
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2020/6/9
  Time: 13:06
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
<h3>
  <a href="${pageContext.request.contextPath}/book/queryAllBooks">进入书籍展示页面</a>
</h3>
  </body>
</html>
```

#### 2.查询所有书籍

> 进入调用service查询逻辑，返回查询页面

```java
private BookServiceImpl bookService;
@Autowired
public void setBookService(BookServiceImpl bookService) {
    this.bookService = bookService;
}
@RequestMapping("/queryAllBooks")
public String list(Model model){
    List<Book> books = bookService.queryAllBooks();
    model.addAttribute("list",books);
    return "allbooks";
}
```

#### 3.显示所有书籍

> 数据显示，同时写添加删除按钮

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2020/6/9
  Time: 16:01
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>书籍展示</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<div class="container">
    <div class="row clearfix">
        <div class="col-md-3 column" >
            <div class="page-header">
                <h1>
                    <small>书籍列表</small>
                </h1>
            </div>
        </div>
    </div>
    <div class="row">
        <div class="col-md-4 column">
            <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddPage">新增书籍</a>
        </div>
    </div>
    <div class="row clearfix">
        <div class="col-md-12 column">
            <table class="table table-hover table-striped">
                <thead>
                <th>书籍编号</th>
                <th>书籍名称</th>
                <th>书籍数量</th>
                <th>书籍详情</th>
                <th>操作</th>
                </thead>
                <tbody>
                <c:forEach var="book" items="${list}">
                    <tr>
                        <td>${book.bookID}</td>
                        <td>${book.bookName}</td>
                        <td>${book.bookCounts}</td>
                        <td>${book.detail}</td>
                        <td>
                            <a href="${pageContext.request.contextPath}/book/updatePage/${book.bookID}">修改</a>
                            &nbsp;| &nbsp;
                            <a href="">删除</a>
                        </td>
                    </tr>
                </c:forEach>
                </tbody>
            </table>
        </div>
    </div>
</div>
</body>
</html>
```

#### 4.修改书籍

> 首先跳转修改页面，需要将此条数据id传过去，并查找出来进行显示，之后修改之后重定向到

```
@RequestMapping("/updatePage/{id}")
public String updatePage(@PathVariable int id, Model model){
    Book book = bookService.queryBook(id);
    model.addAttribute("book",book);
    return "updateBook";
}
```

> 数据在update.jsp回显，同时隐藏id域

```jsp
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2020/6/10
  Time: 12:20
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<div class="container">
    <div class="row clearfix">
        <div class="col-md-3 column" >
            <div class="page-header">
                <h1>
                    <small>修改书籍</small>
                </h1>
            </div>
        </div>
    </div>
    <form action="${pageContext.request.contextPath}/book/updateBook" method="post">
<%--        传递隐藏域--%>
        <input type="hidden" name="bookID" value="${book.bookID}">
        <div class="form-group">
            <label for="bkname">书籍名称</label>
            <input type="text" class="form-control" id="bkname" placeholder="请输入书籍名称" name="bookName" value="${book.bookName}" required>
        </div>
        <div class="form-group">
            <label>书籍数量</label>
            <input type="text" class="form-control" name="bookCounts"  value="${book.bookCounts}">
        </div>
        <div class="form-group">
            <label>书籍描述</label>
            <input type="text" class="form-control" name="detail"  value="${book.detail}">
        </div>
        <div class="form-group">
            <input type="submit" class="form-control" value="修改" required>
        </div>
    </form>
</div>
</body>
</html>
```

> 修改数据，同时重定向到查询书籍，显示所有书籍页面

```java
@RequestMapping("/updateBook")
public String updatetBook(Book book){
    System.out.println("udpateBook-->"+book);
    bookService.updateBook(book);
    return "redirect:/book/queryAllBooks";
}
```

#### 5.增加书籍

> 页面

```jsp
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2020/6/10
  Time: 10:57
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<div class="container">
    <div class="row clearfix">
        <div class="col-md-3 column" >
            <div class="page-header">
                <h1>
                    <small>新增书籍</small>
                </h1>
            </div>
        </div>
    </div>
    <form action="${pageContext.request.contextPath}/book/insertBook" method="post">
        <div class="form-group">
            <label for="bkname">书籍名称</label>
            <input type="text" class="form-control" id="bkname" placeholder="请输入书籍名称" name="bookName" required>
        </div>
        <div class="form-group">
            <label>书籍数量</label>
            <input type="text" class="form-control" name="bookCounts" required>
        </div>
        <div class="form-group">
            <label>书籍描述</label>
            <input type="text" class="form-control" name="detail" required>
        </div>
        <div class="form-group">
            <input type="submit" class="form-control" value="添加" required>
        </div>
    </form>
</div>
</body>
</html>
```

> 控制器代码

```java
@RequestMapping("/toAddPage")
public String toAddPage(Model model){
    return "addBook";
}
@RequestMapping("/insertBook")
public String insertBook(Book book){
    System.out.println("addbook-->"+book);
    bookService.insertBook(book);
    return "redirect:/book/queryAllBooks";
}
```

#### 6.删除书籍

```java
@RequestMapping("/deleteBook/{id}")
public String deleteBook(@PathVariable int id){
    bookService.deleteBookById(id);
    return "redirect:/book/queryAllBooks";
}
```

#### 7.查询书籍

##### 页面

```xml
<form action="" method="" class="form-inline">
    <input type="text" placeholder="请输入查询书籍名称" name="queryBookName" class="form-control">
    <input type="submit" value="查询" class="btn btn-primary">
</form>
```

```
@RequestMapping("/queryBookByName")
public String queryBook(String queryBookName,Model model){
    List<Book> list = bookService.queryBookByName(queryBookName);
    model.addAttribute("list",list);
    return "allbooks";
}
```

#### 注意点

* 在projectStrucure中导入需要的jar包，在web-inf目录下，和classes同目录
* 注意在修改的时候，数据回显不显示id属性，因此我们应该在页面隐藏id属性
* 注意使用rest风格的时候，用form表单提交要求name与controller中方法参数一致，其他情况下使用${name}，那么/{name}接受

