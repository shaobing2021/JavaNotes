### servlet

* 概念：运行在服务器端的小程序
	* Servlet就是一个接口，定义了Java类被浏览器访问到(tomcat识别)的规则。

### Http

[HTTP常见面试题](https://www.cnblogs.com/Java3y/p/8444033.html)

### Request

`request.setCharacterEncoding("utf-8");`

* 请求转发：一种在服务器内部的资源跳转方式

```java
1. 步骤：
			1. 通过request对象获取请求转发器对象：RequestDispatcher getRequestDispatcher(String path)
			2. 使用RequestDispatcher对象来进行转发：forward(ServletRequest request, ServletResponse response) 

		2. 特点：
			1. 浏览器地址栏路径不发生变化
			2. 只能转发到当前服务器内部资源中。
			3. 转发是一次请求
  3. 共享数据：
			* 域对象：一个有作用范围的对象，可以在范围内共享数据
			* request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
			* 方法：
				1. void setAttribute(String name,Object obj):存储数据
				2. Object getAttitude(String name):通过键获取值
				3. void removeAttribute(String name):通过键移除键值对

		4. 获取ServletContext：
			* ServletContext getServletContext()
  
    //登录成功
	            //存储数据
	            req.setAttribute("user",user);
	            //转发
	            req.getRequestDispatcher("/successServlet").forward(req,resp);
```
### Response

 `response.setContentType("text/html;charset=utf-8");`

重定向：资源跳转的方式

 `response.sendRedirect("/day15/responseDemo2");`

### Cookie

客户端会话技术，将数据保存到客户端

### Session

* Session的实现是依赖于Cookie的。

 5. session的特点
	 1. session用于存储一次会话的多次请求的数据，存在服务器端
	 2. session可以存储任意类型，任意大小的数据

	* session与Cookie的区别：
		1. session存储数据在服务器端，Cookie在客户端
		2. session没有数据大小限制，Cookie有
		3. session数据安全，Cookie相对于不安全