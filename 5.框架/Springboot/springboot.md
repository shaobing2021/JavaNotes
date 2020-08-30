## Springboot集成校验







[Springboot官方文档](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/htmlsingle/)

#### pom.xml

```java
//控制版本与打包等内容
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
</parent>
//实现http接口，包含了springmvc，使用tomcat作为默认服务器
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
//用于测试
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
//热部署
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
</dependency>
```

#### 创建springBoot的两种方式

方式1：从官网

方式2：从IDEA上

![image-20200709211840398](https://gitee.com/shaobing2021/typora/raw/master/img/20200728100819.png)

#### Springboot web实战

前端问题：

复习前端知识，以及几个前端框架

根据读源码可以发现resources四种可以放置静态资源的方式

public用于放公共资源

resources优先级>static>public

SpringMvc核心：WebMvcAutoConfiguration

导入一个依赖start，一定存在对应properties

##### 登录页面

1.前端页面：bootstrap+theymleaf

```html
//1.需要导入的xmlns
<html lang="en" xmlns:th="http://www.thymeleaf.org">
//2.改写bootstarp实现国际化
		<a class="btn btn-sm" th:href="@{/index.html(l='zh_CN')}">中文</a>
			<a class="btn btn-sm" th:href="@{/index.html(l='en_US')}">English</a>
		</form>
```

2.重写视图适配器

```java
package com.roosevelt.config;

import org.springframework.boot.autoconfigure.web.servlet.WebMvcRegistrations;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
        registry.addViewController("/index.html").setViewName("index");
        registry.addViewController("/main.html").setViewName("dashboard");
    }
    @Bean
    public LocaleResolver localeResolver(){
        return new MylocaleResolver();
    }
}

```

3.重写LocaleResolver，实现国际化

```java
    @Override
    public Locale resolveLocale(HttpServletRequest request) {
        String language = request.getParameter("l");
        Locale locale = Locale.getDefault();
        if (!StringUtils.isEmpty(language)){
            String[] split = language.split("_");
            locale = new Locale(split[0], split[1]);
        }
        return locale;
    }
```

4.Controller中逻辑以及代码，判断用户名密码是否正确，跳转成功页面，设置session，便于后续操作

```java
@Controller
@RequestMapping("/user")
public class LoginController {
    @RequestMapping("/login")
    //@ResponseBody
    public String login(@RequestParam("username") String username,
                        @RequestParam("password") String password,
                        Model model){
        if ("罗争".equals(username) &&"123456".equals(password)) {
             session.setAttribute("loginUser",username);
            return "redirect:/main.html";
        }else {
            model.addAttribute("msg","用户名或密码错误");
            return "index";
        }
    }
}
```

5.在MyMvcConfig中添加拦截器以及需要拦截的资源

```java
@Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHanlerInterceptor())
                .addPathPatterns("/**").
                excludePathPatterns("/index.html","/","/user/login","/js/**","/css/**","/img/**");
    }
```

##### 查询员工列表

获取员工列表然后放入request中获取

##### 增加员工列表

添加员工列表

修改员工列表*

先要查询该员工，在进行修改，之后相当于调用保存方法进行修改。这里由于增加时候id自增，要修改

##### 删除员工列表

删除员工列表然后在获取

##### 404页面

仅需在templates页面下增加一个error文件夹，放入404页面

##### 注销

点击注销，controller消除session即可

##### 前端框架推荐

bootstrap、layui、semantic-ui

栅格系统

后台模板：x-admin

#### SpringBoot整合

建议重新学数据库连接池

##### 整合JDBC**

1.新建项目需要选择Web、JDBCAPI以及Mysql Server Driver

2.配置yaml，注意时区问题

```xml
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/mybatis01?serverTimezone=UTC
    driver-class-name: com.mysql.jdbc.Driver
//配置数据源
	type: com.alibaba.druid.pool.DruidDataSource
```

3.测试

```java
@Controller
public class JDBCController {
    @Autowired
    JdbcTemplate jdbcTemplate;
    @RequestMapping("/userList")
    public List<Map<String,Object>> accountList(){
        String sql = "select * from account";
        List<Map<String, Object>> maps = jdbcTemplate.queryForList(sql);
        System.out.println(maps);
        return maps;
    }
}
```

注意此处数据库连接池数据源分多种dhcp，c3p0，druid等等，阅读源码，写一个

复习动态sql语句

##### Druid后台监控

https://blog.csdn.net/Exception_M/article/details/104833841?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-12&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-12

添加依赖

```xml
<dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
```

```java
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/mybatis01?serverTimezone=UTC
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      initial-size: 5 #连接池初始化大小
      min-idle: 10 #最小空闲连接数
      max-active: 20 #最大连接数
      # 配置监控统计拦截的 Filter，去掉后监控界面 SQL 无法统计，wall 用于防火墙
      filters: stat,wall,log4j
      web-stat-filter:
        enabled: true
        url-pattern: /*
        exclusions: "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*" #不统计这些请求数据
      stat-view-servlet: #访问监控网页的登录用户名和密码
        enabled: true
        url-pattern: /druid/*
        reset-enable: false
        #        allow: 127.0.0.1
        login-username: druid
        login-password: druid
```

##### 整合Mybatis

1.导入依赖

```java
<dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        
<dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.2</version>
 </dependency>
        
         <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
```



```xml
<dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.2</version>
        </dependency>
```

2.yml中配置mybatis

```
mybatis:
//扫描的实体类
  type-aliases-package: com.roosevelt.pojo
 //扫描的xml文件
  mapper-locations: classpath:mybatis/mapper/*.xml
```

3.编写Dao接口

```java
@Mapper
@Repository
public interface AccountMapper {
    List<Account> queryAccountList();
    Account queryAccountById(int id);
    int addAccount(Account account);
    int updateAccount(Account account);
    int deleteAccount(int id );
}
```

4.配置Mapper

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
        //
<mapper namespace="com.roosevelt.mapper.AccountMapper">
//id是干嘛的？resultType是返回类型
    <select id="queryAccountList" resultType="Account">
        select * from account
    </select>
</mapper>
```

#### Spring Security

授权，身份验证

##### 整合thymeleaf

选择web项目，导入thymeleaf

```
<!-- https://mvnrepository.com/artifact/org.thymeleaf/thymeleaf-spring5 -->
<dependency>
<groupId>org.thymeleaf</groupId>
<artifactId>thymeleaf-spring5</artifactId>
</dependency>
<dependency>
<groupId>org.thymeleaf.extras</groupId>
<artifactId>thymeleaf-extras-java8time</artifactId>
</dependency>
<scope>compile</scope>
<scope>compile</scope>
<version>3.0.4.RELEASE</version>
<version>3.0.11.RELEASE</version>
```

关闭模板引擎

```
spring.thymeleaf.cache=false
```

导入素材，编写controller层

```java
package com.roosevelt.Controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class RouterController {
    @RequestMapping({"/","/index"})
    public String index(){
        return "index";
    }
    @RequestMapping("toLogin")
    public String toLogin(){
        return "views/login";
    }
    @RequestMapping("/level1/{id}")
    public String level1(@PathVariable("id") int id){
        return "views/level1/"+id;
    }

    @RequestMapping("/level2/{id}")
    public String level2(@PathVariable("id") int id){
        return "views/level2/"+id;
    }

    @RequestMapping("/level3/{id}")
    public String level3(@PathVariable("id") int id){
        return "views/level3/"+id;
    }
}

```

config配置

```java
package com.roosevelt.config;

import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    //授权
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests().antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("vip1")
                .antMatchers("/level2/**").hasRole("vip2")
                .antMatchers("/level3/**").hasRole("vip3");
        http.formLogin();
        http.logout().logoutSuccessUrl("/");

    }
    //认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                .withUser("罗争").password(new BCryptPasswordEncoder().encode("123")).roles("vip1","vip2")
                .and()
                .withUser("yi").password(new BCryptPasswordEncoder().encode("123")).roles("vip1");
    }
}
```

springSecurity和thymeleaf整合依赖

```
 <dependency>
            <groupId>org.thymeleaf.extras</groupId>
            <artifactId>thymeleaf-extras-springsecurity5</artifactId>
            <version>3.0.4.RELEASE</version>
        </dependency>
```

核心四个功能：

授权、验证、记住我、防止权限攻击、加密

#### Shrio

##### 快速入门

依赖网址：shiro官网找

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springboot-shrio</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <artifactId>helloshiro</artifactId>
<dependencies>
    <!-- https://mvnrepository.com/artifact/org.apache.shiro/shiro-core -->
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-core</artifactId>
        <version>1.5.2</version>
    </dependency>
    <!-- configure logging -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.21</version>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
</dependencies>

</project>
```

配置log4j.properties以及shiro.ini

```
#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.
#
log4j.rootLogger=INFO, stdout

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m %n

# General Apache libraries
log4j.logger.org.apache=WARN

# Spring
log4j.logger.org.springframework=WARN

# Default Shiro logging
log4j.logger.org.apache.shiro=INFO

# Disable verbose logging
log4j.logger.org.apache.shiro.util.ThreadContext=WARN
log4j.logger.org.apache.shiro.cache.ehcache.EhCache=WARN
```

```
#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.
#
# =============================================================================
# Quickstart INI Realm configuration
#
# For those that might not understand the references in this file, the
# definitions are all based on the classic Mel Brooks' film "Spaceballs". ;)
# =============================================================================

# -----------------------------------------------------------------------------
# Users and their assigned roles
#
# Each line conforms to the format defined in the
# org.apache.shiro.realm.text.TextConfigurationRealm#setUserDefinitions JavaDoc
# -----------------------------------------------------------------------------
[users]
# user 'root' with password 'secret' and the 'admin' role
root = secret, admin
# user 'guest' with the password 'guest' and the 'guest' role
guest = guest, guest
# user 'presidentskroob' with password '12345' ("That's the same combination on
# my luggage!!!" ;)), and role 'president'
presidentskroob = 12345, president
# user 'darkhelmet' with password 'ludicrousspeed' and roles 'darklord' and 'schwartz'
darkhelmet = ludicrousspeed, darklord, schwartz
# user 'lonestarr' with password 'vespa' and roles 'goodguy' and 'schwartz'
lonestarr = vespa, goodguy, schwartz

# -----------------------------------------------------------------------------
# Roles with assigned permissions
# 
# Each line conforms to the format defined in the
# org.apache.shiro.realm.text.TextConfigurationRealm#setRoleDefinitions JavaDoc
# -----------------------------------------------------------------------------
[roles]
# 'admin' role has all permissions, indicated by the wildcard '*'
admin = *
# The 'schwartz' role can do anything (*) with any lightsaber:
schwartz = lightsaber:*
# The 'goodguy' role is allowed to 'drive' (action) the winnebago (type) with
# license plate 'eagle5' (instance specific id)
goodguy = winnebago:drive:eagle5
```

需要更高的版本

```
 <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>
```

##### web整合shiro

1.导入依赖

```
<!-- https://mvnrepository.com/artifact/org.apache.shiro/shiro-spring-boot-web-starter -->
 <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring</artifactId>
            <version>1.4.0</version>
        </dependency>

```

2.ShiroConfig

```
package com.roosevelt.config;

import org.apache.catalina.User;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ShiroConfig {
    //1.创建realm对象
    @Bean
    public UserRealm userRealm(){
        return new UserRealm();
    }
    //2.创建securityManager对象
    @Bean(name = "securityManger")
    public DefaultWebSecurityManager getdefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(userRealm);
        return securityManager;
    }
    //3.创建factoryBean
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        bean.setSecurityManager(defaultWebSecurityManager);
        return bean;
    }
}
```

3.UserRealm

```
package com.roosevelt.config;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

public class UserRealm extends AuthorizingRealm {
    //执行授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行授权");
        return null;
    }
    //执行认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行验证");
        return null;
    }
}

```

##### shiro整合Mybatis

1.导入依赖

```
<dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        
<dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.2</version>
 </dependency>
        
         <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
```

2.配置数据库以及mapper扫描的包

```
spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/mybatis01?serverTimezone=UTC
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      initial-size: 5 #连接池初始化大小
      min-idle: 10 #最小空闲连接数
      max-active: 20 #最大连接数
      # 配置监控统计拦截的 Filter，去掉后监控界面 SQL 无法统计，wall 用于防火墙
      filters: stat,wall,log4j
      web-stat-filter:
        enabled: true
        url-pattern: /*
        exclusions: "*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*" #不统计这些请求数据
      stat-view-servlet: #访问监控网页的登录用户名和密码
        enabled: true
        url-pattern: /druid/*
        reset-enable: false
        #        allow: 127.0.0.1
        login-username: druid
        login-password:
mybatis:
 type-aliases-package: com.roosevelt.pojo
 mapper-locations: classpath:mapper/*.xml
```

3.编写service层和mapper层，查询到user账号密码

4.查询账号密码

```
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("执行验证");
        UsernamePasswordToken usertoken = (UsernamePasswordToken) token;
        Account account = accountService.queryAccountByName(usertoken.getUsername());
        if (account==null){
            return null;
        }
        return new SimpleAuthenticationInfo(account,account.getName(),"");
    }
}
```

5.认证完成进行授权

```
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行授权");
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        //info.addStringPermission("user:add");
        Subject subject = SecurityUtils.getSubject();
        Account currentAccount = (Account) subject.getPrincipal();
        info.addStringPermission(currentAccount.getPerms());
        return info;
    }
```

6.进行业务跳转

```
@Configuration
public class ShiroConfig {
    //1.创建realm对象
    @Bean
    public UserRealm userRealm(){
        return new UserRealm();
    }
    //2.创建securityManager对象
    @Bean(name = "securityManager")
    public DefaultWebSecurityManager getdefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(userRealm);
        return securityManager;
    }
    //3.创建factoryBean
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("securityManager") DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        bean.setSecurityManager(defaultWebSecurityManager);
        LinkedHashMap<String, String> filterMap = new LinkedHashMap<>();
        filterMap.put("/user/add","perms[user:add]");
        filterMap.put("/user/update","perms[user:update]");
        bean.setFilterChainDefinitionMap(filterMap);

        bean.setLoginUrl("/toLogin");
        bean.setUnauthorizedUrl("/noauth");
        return bean;
    }
}
```

##### shiro整合thymeleaf

判断权限，只显示权限可以显示的页面

判断是否登录，登录就不显示登录按钮

1.导入依赖

```
<!-- https://mvnrepository.com/artifact/com.github.theborakompanioni/thymeleaf-extras-shiro -->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>
```

2.判断是否有条件等

```xml
<div th:if="${session.loginUser==null}">
    <a th:href="@{/toLogin}">登录</a>
</div>
<p th:text="${msg}"></p>
<div shiro:hasPermission="user:add">
<a th:href="@{/user/add}">add</a><br>
</div>
<div shiro:hasPermission="user:update">
<a th:href="@{/user/update}">update</a><br>
</div>
```

3.验证成功，则添加session

```java
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("执行验证");
        UsernamePasswordToken usertoken = (UsernamePasswordToken) token;
        Account account = accountService.queryAccountByName(usertoken.getUsername());
        if (account==null){
            return null;
        }
        Subject currentSubject = SecurityUtils.getSubject();
        Session session = currentSubject.getSession();
        session.setAttribute("loginUser",account);
        return new SimpleAuthenticationInfo(account,account.getName(),"");
    }
}
```

#### Swagger

简介.前后端分离

前端测试后端接口

后端提供接口

依赖

```
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>

```

3.编写一个hello工程

4.配置swagger，编写config

```
@Configuration
//可以加载进配置文件
@EnableSwagger2
public class SwaggerConfig {
}
```

5.swagger核心配置

```
package com.roosevelt.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.ArrayList;

@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.roosevelt.controller"))

                .build();
    }
    private ApiInfo apiInfo(){
       Contact DEFAULT_CONTACT = new Contact("Roosevlet", "", "40300159@qq.com");
        return  new ApiInfo("Roosevelt Documentation",
                "Api Documentation",
                "1.0",
                "urn:tos",
                DEFAULT_CONTACT,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList());
    }
}
```

#### 任务

##### 异步任务

开启异步功能

```
@EnableAsync
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

编写异步代码

```
@Service
public class AsyncService{
    @Async
    public void Hello(){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("数据正在处理");
    }
}
```

##### 邮件任务

1.开启smtp->邮箱->设置->账户->开启pop3

2.编写代码

```
package com.roosevelt.demo;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSenderImpl;
import org.springframework.mail.javamail.MimeMailMessage;
import org.springframework.mail.javamail.MimeMessageHelper;

import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import java.io.File;

@SpringBootTest
class DemoApplicationTests {
    @Autowired
    JavaMailSenderImpl mailSender;

    @Test
    void contextLoads() {
        SimpleMailMessage mailMessage = new SimpleMailMessage();
        mailMessage.setSubject("罗争你好");
        mailMessage.setText("主题");
        mailMessage.setTo("172140553@qq.com");
        mailMessage.setFrom("403001592@qq.com");
        mailSender.send(mailMessage);
    }
    @Test
    void contextLoads1() throws MessagingException {
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage,true);
        helper.setSubject("罗争你好plus");
        helper.setText("<h1>hello</h1>",true);
        helper.addAttachment("1.jpg",new File("F:\\java\\1.jpg"));
        helper.setTo("172140553@qq.com");
        helper.setFrom("403001592@qq.com");

        mailSender.send(mimeMessage);
    }

}
```

##### 定时任务

1.开启定时功能注解

```
cron表达式
```

```
@EnableAsync
@EnableScheduling
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}

```

2.定时代码

注意学习cron表达式

```java
@Service
public class ScheduledService {
    @Scheduled(cron表达式)
    public void hello(){
        System.out.println("hello,你执行了");
    }
}
```





duboo和zookeeper包

apache-zookeeper-3.6.1-bin.tar

F:\java\java软件\zookeeper-3.4.14

dubo和zookeeper依赖

```
<!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-spring-boot-starter -->
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>2.7.6</version>
</dependency>


<!-- https://mvnrepository.com/artifact/com.github.sgroschupf/zkclient -->
<dependency>
    <groupId>com.github.sgroschupf</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.1</version>
</dependency>

```

防止和springboot冲突

