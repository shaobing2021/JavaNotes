
# SpringMVc
[dispatcherServlet](https://blog.csdn.net/u010310183/article/details/89534309)


### 配置

#### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
git 
    <groupId>org.example</groupId>
    <artifactId>springmvc-study</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>springmvc-1-servlet</module>
        <module>springmvc-2</module>
        <module>springmvc-3-annotation</module>
        <module>springmvc-4-ajax</module>
    </modules>
<!--一开始导入的几个包-->
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
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

<!--        导入jackson  实现json数据的封装拆分-->
        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.10.0</version>
        </dependency>

<!--fastjson-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.60</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
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
        </resources></build>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
</project>
```

#### applicationContext.xml

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

    <!--自动扫描包，让该包下的注解生效，由IOC统一管理-->
    <context:component-scan
            base-package="com.roosevelt.controller"/>
    <!--解决json数据可能出现的乱码问题，造成原因，ObjectMapper-->
    <mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="UTF-8"/>
            </bean>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper">
                    <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                        <property name="failOnEmptyBeans" value="false"/>
                    </bean>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
    <!--
      .css,.js,.html,.mp3,.mp4-->
    <mvc:default-servlet-handler/>
    <!--处理器映射适配器,支持mvc注解驱动，使@RequestMapping生效
        自动注入映射器和适配器实例-->
<!--    <mvc:annotation-driven/>-->
    <!--视图解析器：模板引擎Thymeleaf，Freemarker-->
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

#### web.xml

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
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--         -->
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
</web-app>
```

#### springmvc-servlet.xml

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

    <!--自动扫描包，让该包下的注解生效，由IOC统一
    管理-->
    <context:component-scan
            base-package="com.roosevelt.controller"/>
    <!--解决json数据可能出现的乱码问题，造成原因，ObjectMapper-->
    <mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="UTF-8"/>
            </bean>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper">
                    <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                        <property name="failOnEmptyBeans" value="false"/>
                    </bean>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
    <!--
      .css,.js,.html,.mp3,.mp4-->
    <mvc:default-servlet-handler/>
    <!--处理器映射适配器,支持mvc注解驱动，使@RequestMapping生效
        自动注入映射器和适配器实例-->
<!--    <mvc:annotation-driven/>-->
    <!--视图解析器：模板引擎Thymeleaf，Freemarker-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!--        前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--        后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
    <!--    <bean id="/hello" class="com.roosevelt.controller.HelloController"/>-->

<!--拦截器配置-->
<mvc:interceptors>
    <mvc:interceptor>
<!--        包括这个请求下面所有的请求-->
        <mvc:mapping path="/**" />
<!--        用哪个类过滤-->
        <bean  class="com.roosevelt.config.LoginInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
</beans>
```

### 1.简介

[SpringMvc官方文档](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc)

[老版本](https://docs.spring.io/spring/docs/4.3.24.RELEASE/spring-framework-reference/html/)

MVC:

* 模型
* 视图
* 控制器

根据POJO类用途的不同，可以把POJO类转化为VO, PO, DTO

`**VO: Value Object 值对象**`
通常用于业务层之间的数据传递，和 PO 一样也是仅仅包含数据而已。但应是抽象出的业务对象 , 可以和表对应 , 也可以不 , 这根据业务的需要 。用 new 关键字创建，由 GC 回收的
PO: Persistant Object 持久对象
PO为持久层对象，那么就作用在持久层，即存储数据的地方。那么就不可避免要与数据库相关联。PO可以看成是与数据库中的表相映射的java对象。PO中应该不包含任何和对数据库的操作。
`DTO: Data Transfer Object 数据传输对象`
泛指用于展示层（view）与服务层（service）之间的数据传输对象。

`DAO(Database Access Object) 数据访问对象`
与数据库相关联的类通常被称为DAO，此对象用于访问数据库。通常和 PO 结合使用， DAO 中包含了各种数据库的操作方法。通过它的方法 , 结合 PO 对数据库进行相关的操作。夹在业务逻辑与数据库资源中间。配合 VO, 提供数据库的增删改查操作。

`JavaBean`
JavaBean是一个遵循特定写法的Java类，是一种Java语言编写的可重用组件，它的方法命名，构造及行为必须符合特定的约定：
1、这个类必须具有一个公共的(public)无参构造函数；
2、所有属性私有化（private）；
3、私有化的属性必须通过public类型的方法（getter和setter）暴露给其他程序，并且方法的命名也必须遵循一定的命名规范。
4、这个类应是可序列化的。（比如可以实现Serializable 接口，用于实现bean的持久性）

### 2.回顾servlet

#### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>springmvc-study</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>springmvc-servlet1</module>
    </modules>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
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
    </dependencies>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
</project>
```

#### HelloServlet

```java
package com.roosevelt.servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String method =req.getParameter("method");
        if (method.equals("add")){
            req.getSession().setAttribute("msg","执行了add");
        }
        if (method.equals("delete")){
            req.getSession().setAttribute("msg","执行了delete");
        }
        req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req,resp);
    }
}
```

#### 配置映射

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>com.roosevelt.servlet.HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
    <session-config>
        <session-timeout>20</session-timeout>
    </session-config>
</web-app>
```

#### MVC框架

* 将url映射到java类或java类的方法
* 封装用户提交的数据
* 处理请求--调用相关的业务处理--封装响应数据
* 将响应的数据进行渲染，jsp/html等表示层数据

### 3.第一个servlet程序

#### 实现Contoller接口流程

注意在架构中添加lib

可查看流程图

1. web.xml中配置`DispatcherServlet`过滤所有路径
   1. `DispatcherServlet`表示前置控制器，url可以拆成三部分http://localhost:8080/helloMvc2/hello   服务器域名+站点名springmvc+控制器hello
   2. 即请求位于服务器上的hello控制器
2. 根据url找到springmvc-servlet.xml中Handler处理器，即HelloController
3. 控制器返回modelAndView，解析视图名字，找到视图

![img](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7IicxBZbkh0D4dJJiaXSzGEXyzsXDPy7oAJFsBvvBibiaFWpSp75vFIEOCBm7wnt4JKXJCHB9MflUycKw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

#### web.xml

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
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
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
</web-app>
```

spring-servelt.xml

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




    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <bean id="/hello" class="com.roosevelt.HelloController"/>
</beans>

```

#### springmvc-servlet.xml

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



<!--处理器映射器，默认由-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
<!--处理器适配器-->
        <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
<!--视图解析器：模板引擎Thymeleaf，Freemarker-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
<!--        前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
<!--        后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
<!--BeanNameUrlhandlerMapping：bean-->
    <bean id="/hello" class="com.roosevelt.HelloController"/>
</beans>
```

#### Hellocontroller.java

```java
package com.roosevelt;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class HelloController implements Controller {
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        //ModelAndView 模型和视图
        ModelAndView mv = new ModelAndView();

        //封装对象，放在ModelAndView中。Model
        mv.addObject("msg","HelloSpringMVC!");
        //封装要跳转的视图，放在ModelAndView中
        mv.setViewName("hello"); //: /WEB-INF/jsp/hello.jsp
        return mv;
    }
}
```

#### 注意404错误

* 在project structure--->在web-inf目录下新建lib-->导入所有包
* 建议直接maven创建web项目

#### 缺陷

一个控制器只有一个方法，如果要多个方法则需要定义多个Controller；定义的方式比较麻烦

### 4.基于注解配置MVC

* @RequestMapping：可以接收所有参数
* @Post，@Get,@Delete,@put

#### 步骤

1. 新建web项目
2. 导入jar包
3. 编写web.xml
4. 编写springmvc配置文件
5. 编写controller
6. 完成前端视图和controller之间的对应
7. 测试运行调试

#### web.xml

> 注册dispathservlet

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
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
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
</web-app>
```

#### springmvc-serlet.xml

> 开启注解
>
> 最重要的三大组件：处理器映射器，处理器适配器，视图解析器，
>
> 前两个只要开启注解驱动即可

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

<!--自动扫描包，让该包下的注解生效，由IOC统一
管理-->
<context:component-scan
        base-package="com.roosevelt.controller"/>
<!--    让springmvc不处理静态资源  .css,.js,.html,.mp3,.mp4，可以省略-->
<mvc:default-servlet-handler/>
<!--处理器映射适配器,支持mvc注解驱动，使@RequestMapping生效
    自动注入映射器和适配器实例，可以省略-->
<mvc:annotation-driven/>
    <!--视图解析器：模板引擎Thymeleaf，Freemarker-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="
              ">
        <!--        前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--        后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
<!--    <bean id="/hello" class="com.roosevelt.controller.HelloController"/>-->
</beans>
```

#### controller

```java
package com.roosevelt.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
//如果先注解admin，则需先访问类上的
@RequestMapping("/admin")

public class HelloController {
    @RequestMapping(path = "/hello")
    public String hello(Model model){
        //封装数据
        model.addAttribute("msg","Hello,SpringMvc");
        return "hello";//被视图解析器解析
    }

}
```

### 5.Restful

#### 原始方式

http://localhost:8080/add?a=1&b=2

```java
@Controller
public class RestFulController {
    //原来的方式
    @RequestMapping("/add")
    public String test(int a, int b, Model model){
        int res = a+b;
        model.addAttribute("msg",res);
        return "test";
    }
}
```

#### RestFul风格1

```java
package com.roosevelt.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
public class RestFulController {
    //restful风格
    @RequestMapping(value = "/add/{a}/{b}",method = RequestMethod.DELETE)
    public String test(@PathVariable  int a, @PathVariable int b, Model model){
        int res = a+b;
        model.addAttribute("msg",res);
        return "test";
    }
}
```

#### RestFul风格2

```java
@RequestMapping(value = "/add/{a}/{b}")
@GetMapping
public String test(@PathVariable  int a, @PathVariable int b, Model model){
    int res = a+b;
    model.addAttribute("msg",res);
    return "test";
}
```

#### RestFul风格3

此处使用value或者path均可

```java
@PostMapping(value = "/add/{a}/{b}")
public String test2(@PathVariable  int a, @PathVariable int b, Model model){
    int res = a+b;
    model.addAttribute("msg",res);
    return "test";
}
```



```
return "foward:/WEB-INF/jsp/test.jsp"   //转发  地址栏不变
return “redirect:/index.jsp"   //重定向  ，地址栏改变
```

#### Model

* Model适合用于存储数据
* ModelMap继承LinkedMap，具有LinkedMap方法和特性
* ModelAndView可以在存储数据同时，设置返回逻辑视图

### 6.乱码问题

#### 通常情况下

web.xml中配置

```xml
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
```

#### Tomcat乱码

```xml
<Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
          connectionTimeout="20000"
          redirectPort="8443" />
```

#### 自写Filter

```java
package com.kuang.filter;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.Map;

/**
* 解决get和post请求 全部乱码的过滤器
*/
public class GenericEncodingFilter implements Filter {

   @Override
   public void destroy() {
  }

   @Override
   public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
       //处理response的字符编码
       HttpServletResponse myResponse=(HttpServletResponse) response;
       myResponse.setContentType("text/html;charset=UTF-8");

       // 转型为与协议相关对象
       HttpServletRequest httpServletRequest = (HttpServletRequest) request;
       // 对request包装增强
       HttpServletRequest myrequest = new MyRequest(httpServletRequest);
       chain.doFilter(myrequest, response);
  }

   @Override
   public void init(FilterConfig filterConfig) throws ServletException {
  }

}

//自定义request对象，HttpServletRequest的包装类
class MyRequest extends HttpServletRequestWrapper {

   private HttpServletRequest request;
   //是否编码的标记
   private boolean hasEncode;
   //定义一个可以传入HttpServletRequest对象的构造函数，以便对其进行装饰
   public MyRequest(HttpServletRequest request) {
       super(request);// super必须写
       this.request = request;
  }

   // 对需要增强方法 进行覆盖
   @Override
   public Map getParameterMap() {
       // 先获得请求方式
       String method = request.getMethod();
       if (method.equalsIgnoreCase("post")) {
           // post请求
           try {
               // 处理post乱码
               request.setCharacterEncoding("utf-8");
               return request.getParameterMap();
          } catch (UnsupportedEncodingException e) {
               e.printStackTrace();
          }
      } else if (method.equalsIgnoreCase("get")) {
           // get请求
           Map<String, String[]> parameterMap = request.getParameterMap();
           if (!hasEncode) { // 确保get手动编码逻辑只运行一次
               for (String parameterName : parameterMap.keySet()) {
                   String[] values = parameterMap.get(parameterName);
                   if (values != null) {
                       for (int i = 0; i < values.length; i++) {
                           try {
                               // 处理get乱码
                               values[i] = new String(values[i]
                                      .getBytes("ISO-8859-1"), "utf-8");
                          } catch (UnsupportedEncodingException e) {
                               e.printStackTrace();
                          }
                      }
                  }
              }
               hasEncode = true;
          }
           return parameterMap;
      }
       return super.getParameterMap();
  }

   //取一个值
   @Override
   public String getParameter(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       if (values == null) {
           return null;
      }
       return values[0]; // 取回参数的第一个值
  }

   //取所有值
   @Override
   public String[] getParameterValues(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       return values;
  }
}
```

### 7.JSON

#### 依赖包

```
<!--        导入jackson  实现json数据的封装拆分-->
        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.10.0</version>
        </dependency>

<!--fastjson-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.60</version>
        </dependency>
```

> 两个都可以使用，fastjson不需要实例化，因此性能更好

#### 一般情况下

**User(id=1, name=??, sex=?)**

```
 @RequestMapping("/j1")
    @ResponseBody//它就不会走视图解析器，会直接返回一个字符串
    public String json1() throws JsonProcessingException {
        User user = new User(1, "罗争", "男");
//        user.toString();
        ObjectMapper mapper = new ObjectMapper();
        String str = mapper.writeValueAsString(user);
        return user.toString();

    }
```

#### 传递Json

处理json乱码问题

```xml
<mvc:annotation-driven>
   <mvc:message-converters register-defaults="true">
       <bean class="org.springframework.http.converter.StringHttpMessageConverter">
           <constructor-arg value="UTF-8"/>
       </bean>
       <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
           <property name="objectMapper">
               <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                   <property name="failOnEmptyBeans" value="false"/>
               </bean>
           </property>
       </bean>
   </mvc:message-converters>
</mvc:annotation-driven>
```

使用`RestController`注解在`Controller`类上，表示这个类只会返回一个字符串=`Controller`+`ResponseBody`

#### 时间

通常返回的日期格式有问题，想输出需要转换

##### 方式一

```java
@RequestMapping("/j2")
public String json2() throws JsonProcessingException {
    Date date = new Date();
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    return new ObjectMapper().writeValueAsString(sdf.format(date));
}
```

##### 方式二

```java
@RequestMapping("/json4")
public String json4() throws JsonProcessingException {
   ObjectMapper mapper = new ObjectMapper();
   //不使用时间戳的方式
   mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
   //自定义日期格式对象
   SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
   //指定日期格式
   mapper.setDateFormat(sdf);
   Date date = new Date();
   String str = mapper.writeValueAsString(date);
   return str;
}
```

#### Json工具类

```java
package com.roosevelt.utils;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;

import java.text.SimpleDateFormat;
import java.util.Date;

public class JsonUtils {
    public static String getJson(Object object){
        return getJson(object,"");
    }

    public static String getJson(Object object,String dateFormat){
        ObjectMapper mapper = new ObjectMapper();
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
        //自定义日期格式对象
        SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
        //指定日期格式
        mapper.setDateFormat(sdf);

        Date date = new Date();
        try {
            return mapper.writeValueAsString(date);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return null;

    }
}
```

#### fastjson

```xml
<!--        导入jackson  实现json数据的封装拆分-->
        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.10.0</version>
        </dependency>

<!--fastjson-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.60</version>
        </dependency>
```

```java
@RequestMapping("/j4")
public String json4(){
    ArrayList<User> list = new ArrayList<>();
    User user = new User(2, "罗争", "女");
    User user1 = new User(2, "罗争", "女");
    User user2 = new User(2, "罗争", "女");
    list.add(user1);
    list.add(user2);
    list.add(user);
    return JSON.toJSONString(list);
}
```

#### 其他使用

json本身是一个字符串，这里弄成对象

```java
package com.kuang.controller;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import com.kuang.pojo.User;

import java.util.ArrayList;
import java.util.List;

public class FastJsonDemo {
   public static void main(String[] args) {
       //创建一个对象
       User user1 = new User("秦疆1号", 3, "男");
       User user2 = new User("秦疆2号", 3, "男");
       User user3 = new User("秦疆3号", 3, "男");
       User user4 = new User("秦疆4号", 3, "男");
       List<User> list = new ArrayList<User>();
       list.add(user1);
       list.add(user2);
       list.add(user3);
       list.add(user4);

       System.out.println("*******Java对象 转 JSON字符串*******");
       String str1 = JSON.toJSONString(list);
       System.out.println("JSON.toJSONString(list)==>"+str1);
       String str2 = JSON.toJSONString(user1);
       System.out.println("JSON.toJSONString(user1)==>"+str2);

       System.out.println("\n****** JSON字符串 转 Java对象*******");
       User jp_user1=JSON.parseObject(str2,User.class);
       System.out.println("JSON.parseObject(str2,User.class)==>"+jp_user1);

       System.out.println("\n****** Java对象 转 JSON对象 ******");
       JSONObject jsonObject1 = (JSONObject) JSON.toJSON(user2);
       System.out.println("(JSONObject) JSON.toJSON(user2)==>"+jsonObject1.getString("name"));

       System.out.println("\n****** JSON对象 转 Java对象 ******");
       User to_java_user = JSON.toJavaObject(jsonObject1, User.class);
       System.out.println("JSON.toJavaObject(jsonObject1, User.class)==>"+to_java_user);
  }
}
```

### 8.Ajax

#### 配置

```jsp
<script src="${pageContext.request.contextPath}/static/jquery-3.3.1.min.js"></script>
```

#### 页面编写

```
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2020/6/10
  Time: 18:54
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
<%--    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.1.0/jquery.min.js"></script>--%>
    <script src="${pageContext.request.contextPath}/static/jquery-3.3.1.min.js"></script>
    <script type="text/javascript">
      function a() {
        $.post({
          url:"${pageContext.request.contextPath}/t2",
          data:{"name":$("#username").val()},
          success:function (data) {
            alert(data);
          }
        })
      }
    </script>
  </head>
  <body>
用户名:<input type="text" id="username" onblur="a()">
  </body>
</html>
```

#### controller

```java
@RequestMapping("/t2")
public void test2(String name, HttpServletResponse response) throws IOException {
    System.out.println(name);
    response.getWriter().print("true");
}
```

#### 案例

##### jsp

```jsp
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2020/6/11
  Time: 14:53
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
    <script src="${pageContext.request.contextPath}/static/jquery-3.3.1.min.js"></script>
    <script>
        function l1() {
            $.post({
                url:"${pageContext.request.contextPath}/a3",
                data:{"name":$("#username").val()},
                success:function (data) {
                    if (data.toString()==='ok'){
                        $("#userInfo").css("color","green");
                    }else {
                        $("#userInfo").css("color","red");
                    }
                    $("#userInfo").html(data);
                }
            })
        }
        function l2() {
            $.post({
                url:"${pageContext.request.contextPath}/a3",
                data:{"pwd":$("#password").val()},
                success:function (data) {
                    if (data.toString()==='ok'){
                        $("#pwdInfo").css("color","green");
                    }else {
                        $("#pwdInfo").css("color","red");
                    }
                    $("#pwdInfo").html(data);
                }
            })
        }
    </script>
</head>
<body>
<p>
    用户名：<input type="text" id="username" onblur="l1()">
    <span id="userInfo"></span>
</p>
<p>
    密码：<input type="text" id="password" onblur="l2()">
    <span id="pwdInfo"></span>
</p>
</body>
</html>
```

##### controller

```java
package com.roosevelt.controller;

import com.roosevelt.pojo.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

@Controller
public class AjaxController {
    @RequestMapping("/t9")
    public String test(){
        return "hello";
    }
    @RequestMapping("/t2")
    public void test2(String name, HttpServletResponse response) throws IOException {
        System.out.println(name);
        response.getWriter().print("true");
    }
    @RequestMapping("/a2")
    @ResponseBody
    public List<User> a2(){
        ArrayList<User> list = new ArrayList<>();
        list.add(new User("一",12,"男"));
        list.add(new User("二",13,"男"));
        return list;
    }
    @RequestMapping("/a3")
    @ResponseBody
    public String a3(String name ,String pwd){
        String msg = "";
        if (name!=null){
            if ("admin".equals(name)){
                msg = "ok";
            }else {
                msg = "用户名有误";
            }
        }
        if (pwd!=null){
            if ("123".equals(pwd)){
                msg = "ok";
            }else {
                msg = "密码有误";
            }
        }
        return msg;
    }
}
```

### 9.拦截器

* 过滤器：servlet规范，对所有访问资源进行过滤
* 拦截器：  到spring-servlet中

applicationContext/spring-servlet.xml配置

```xml
<!--拦截器配置-->
<mvc:interceptors>
    <mvc:interceptor>
<!--        包括这个请求下面所有的请求-->
        <mvc:mapping path="/**" />
<!--        用哪个类过滤-->
        <bean  class="com.roosevelt.config.MyInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

#### 过滤

```java
package com.roosevelt.config;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class MyInterceptor implements HandlerInterceptor {
    //return true 放行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("prehandle");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
```

##### 验证

> 如果没有登录，无法进入首页
>
> 第一次进首页拦截器判断是否登录，没有登录则跳转登录页面，否则放行
>
> 登录之后，会被拦截，被放行，添加session
>
> 进入首页，进行注销
>
> 注销移除session

1. 编写页面没有登入，无法进入首页

```
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2020/6/11
  Time: 18:56
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>首页</title>
</head>
<body>
<h1><a href="${pageContext.request.contextPath}/gologin">登录</a> </h1>
<h1><a href="${pageContext.request.contextPath}/main">首页</a> </h1>
</body>
</html>
```

2. interceptor

```java
package com.roosevelt.config;

import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
//        判断什么情况下登录
        HttpSession session = request.getSession();
        if (request.getRequestURI().contains("login")){
            return true;
        }
        if (session.getAttribute("loginInfo")!=null){
            return true;
        }
        request.getRequestDispatcher("/WEB-INF/jsp/login1.jsp").forward(request,response);
        return false;
    }
}
```

3. controller

```java
package com.roosevelt.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.http.HttpSession;

@Controller
public class LoginController {
    @RequestMapping("/login")
    public String login(String username, String password, HttpSession session){
        session.setAttribute("loginInfo",username);
        return "hello";
    }
    @RequestMapping("/gologin")
    public String gologin(){
        return "login1";
    }
    @RequestMapping("/main")
    public String main(){
        return "hello";
    }
    @RequestMapping("/goOut")
    public String main(HttpSession session){
        session.removeAttribute("loginInfo");
        return "hello";
    }
}
```

4. 登录页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>登录</title>
</head>
<body>
<h1>登录页面</h1>
<form action="${pageContext.request.contextPath}/login" method="post">
    用户名：<input type="text" name="username"/><br>
    密  码：<input type="text" name="password"/><br>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

5. 首页

```jsp
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2020/6/10
  Time: 19:17
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
hello
<a href="${pageContext.request.contextPath}/goOut">注销</a>
</body>
</html>
```

### 10.文件上传

导入包

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.3</version>
</dependency>
<!--servlet-api导入高版本的-->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
</dependency>
```

上传配置

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

    <!--自动扫描包，让该包下的注解生效，由IOC统一
    管理-->
    <context:component-scan
            base-package="com.roosevelt.controller"/>
    <!--解决json数据可能出现的乱码问题，造成原因，ObjectMapper-->
<!--    <mvc:annotation-driven>-->
<!--        <mvc:message-converters register-defaults="true">-->
<!--            <bean class="org.springframework.http.converter.StringHttpMessageConverter">-->
<!--                <constructor-arg value="UTF-8"/>-->
<!--            </bean>-->
<!--            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">-->
<!--                <property name="objectMapper">-->
<!--                    <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">-->
<!--                        <property name="failOnEmptyBeans" value="false"/>-->
<!--                    </bean>-->
<!--                </property>-->
<!--            </bean>-->
<!--        </mvc:message-converters>-->
<!--    </mvc:annotation-driven>-->
    <!--
      .css,.js,.html,.mp3,.mp4-->
    <mvc:default-servlet-handler/>
    <!--处理器映射适配器,支持mvc注解驱动，使@RequestMapping生效
        自动注入映射器和适配器实例-->
    <mvc:annotation-driven/>
    <!--视图解析器：模板引擎Thymeleaf，Freemarker-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!--        前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--        后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
    <!--    <bean id="/hello" class="com.roosevelt.controller.HelloController"/>-->

    <!--文件上传配置-->
    <bean id="multipartResolver"  class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 请求的编码格式，必须和jSP的pageEncoding属性一致，以便正确读取表单的内容，默认为ISO-8859-1 -->
        <property name="defaultEncoding" value="utf-8"/>
        <!-- 上传文件大小上限，单位为字节（10485760=10M） 可以不配-->
        <property name="maxUploadSize" value="10485760"/>
        <property name="maxInMemorySize" value="40960"/>
    </bean>
</beans>
```

页面

```jsp
<%--
  Created by IntelliJ IDEA.
  User: admin
  Date: 2020/6/11
  Time: 20:19
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
  <form action="${pageContext.request.contextPath}/upload" enctype="multipart/form-data" method="post">
    <input type="file" name="file"/>
    <input type="submit" value="upload">
  </form>
  </body>
</html>
```

controller

```java
package com.roosevelt.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.commons.CommonsMultipartFile;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.*;
import java.net.URLEncoder;

@RestController
public class HelloController {
    @RequestMapping("/upload")
    public String fileUpload(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request) throws IOException {

        //获取文件名 : file.getOriginalFilename();
        String uploadFileName = file.getOriginalFilename();

        //如果文件名为空，直接回到首页！
        if ("".equals(uploadFileName)) {
            return "redirect:/index.jsp";
        }
        System.out.println("上传文件名 : " + uploadFileName);

        //上传路径保存设置
        String path = request.getServletContext().getRealPath("/upload");
        //如果路径不存在，创建一个
        File realPath = new File(path);
        if (!realPath.exists()) {
            realPath.mkdir();
        }
        System.out.println("上传文件保存地址：" + realPath);

        InputStream is = file.getInputStream(); //文件输入流
        OutputStream os = new FileOutputStream(new File(realPath, uploadFileName)); //文件输出流

        //读取写出
        int len = 0;
        byte[] buffer = new byte[1024];
        while ((len = is.read(buffer)) != -1) {
            os.write(buffer, 0, len);
            os.flush();
        }
        os.close();
        is.close();
        return "redirect:/index.jsp";
    }

    /*
     * 采用file.Transto 来保存上传的文件
     */
    @RequestMapping("/upload2")
    public String  fileUpload2(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request) throws IOException {

        //上传路径保存设置
        String path = request.getServletContext().getRealPath("/upload");
        File realPath = new File(path);
        if (!realPath.exists()){
            realPath.mkdir();
        }
        //上传文件地址
        System.out.println("上传文件保存地址："+realPath);

        //通过CommonsMultipartFile的方法直接写文件（注意这个时候）
        file.transferTo(new File(realPath +"/"+ file.getOriginalFilename()));

        return "redirect:/index.jsp";
    }
    @RequestMapping("/test")
    public String test(){
        return "test";
    }
    @RequestMapping(value="/download")
    public String downloads(HttpServletResponse response , HttpServletRequest request) throws Exception{
        //要下载的图片地址
        String  path = request.getServletContext().getRealPath("/upload");
        String  fileName = " .jpg";

        //1、设置response 响应头
        response.reset(); //设置页面不缓存,清空buffer
        response.setCharacterEncoding("UTF-8"); //字符编码
        response.setContentType("multipart/form-data"); //二进制传输数据
        //设置响应头
        response.setHeader("Content-Disposition",
                "attachment;fileName="+ URLEncoder.encode(fileName, "UTF-8"));

        File file = new File(path,fileName);
        //2、 读取文件--输入流
        InputStream input=new FileInputStream(file);
        //3、 写出文件--输出流
        OutputStream out = response.getOutputStream();

        byte[] buff =new byte[1024];
        int index=0;
        //4、执行 写出操作
        while((index= input.read(buff))!= -1){
            out.write(buff, 0, index);
            out.flush();
        }
        out.close();
        input.close();
        return null;
    }
}
```