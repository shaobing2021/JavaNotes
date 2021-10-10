# Web原生组件注入
[web组件文档-features-7.4.1](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.developing-web-applications.embedded-container.servlets-filters-listeners)

## 原生使用Servlet Api

### serlvet
> Servlet类继承
* 开启WebSerlvet注解
* 注意会直接走servlet，没有spring的拦截
```java
@WebServlet(urlPatterns = "/my")
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("hello,servlet");
    }
}

@ServletComponentScan(basePackages = "com.shaobing")
@SpringBootApplication
public class WebMvcApplication {
```

### 过滤器

容器一初始化filter则会init，当请求发送则会进入到过滤器中doFilter方法，当容器结束则webFilter销毁
```java
package com.shaobing.webmvc.servlet;

import lombok.extern.slf4j.Slf4j;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;
@Slf4j
//单*是servlet写法，双**是spring写法
@WebFilter(urlPatterns = {"/css/*","/images/*"})
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        log.info("MyFilter init...");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        log.info("MyFilter doFilter...");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {
        log.info("MyFilter destroy...");
    }
}
```

### 监听器
```java
package com.shaobing.webmvc.servlet;

import lombok.extern.slf4j.Slf4j;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

@Slf4j
@WebListener
public class MyListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        log.info("MyServletContextListener listener...");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        log.info("MyServletContextListener destroy...");
    }
}

```

## 拦截过滤监听器
### 区别
拦截器：面向切面编程，通常使用动态代理实现，只对action对应方法起作用而过滤器对所有的请求起作用，能够访问堆栈中的信息，action上下文

过滤器：过滤掉一些信息，依赖于servlet容器，如未登陆，不放行等操作
### 流程
web容器启动时，
listener init-->filter init-->servlet init-->servlet doGet-->interceptor prehandler-->method-->interceptor posthandler
web容器关闭时
servlet destroy-->filter destroy-->listener destroy
