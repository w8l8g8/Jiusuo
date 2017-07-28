# JSP/Servlet笔记

[TOC]

## 1 Web容器

### 1.1 容器能提供什么？

1. 通信支持
2. 生命周期管理
3. 多线程支持
4. 声明方式实现安全
5. JSP支持

### 1.2 容器处理请求

1. 用户点击一个链接，URL指向一个Servlet，而不是一个静态页面
2. 容器识别出是指向Servlet的请求，所以创建出两个对象**HttpServletResponse** 和 **HttpServletRequest** 
3. 容器根据请求中的URL找出对应的Servlet，为这个请求**创建或分配一个线程**，并将请求和响应对象传递给这个Servlet线程
4. 容器调用Servlet的`service()`方法，根据请求的不同类型会调用`doGet()`和`doPost()`方法
5. `doGet() `方法生成动态页面，填入响应对象，同时容器还有响应对象的一个引用
6. 线程结束，容器把响应对象**转换**为一个HTTP响应把它发回给客户，同时删除请求和响应对象

完整的Servlet示例

```java
// 导入必需的 java 库
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

// 扩展 HttpServlet 类
public class HelloWorld extends HttpServlet {
 
  private String message;

  public void init() throws ServletException
  {
      // 执行必需的初始化
      message = "Hello World";
  }

  public void doGet(HttpServletRequest request,
                    HttpServletResponse response)
            throws ServletException, IOException
  {
      // 设置响应内容类型
      response.setContentType("text/html");

      // 实际的逻辑是在这里
      PrintWriter out = response.getWriter();//在servlet从容器中拿到的响应对象中可以获取一个PrintWriter
      out.println("<h1>" + message + "</h1>");
  }
  
  public void destroy()
  {
      // 什么也不做
  }
}
```

### 1.3 Servlet的映射

一个Servlet有3个名字：客户知道的URL、部署名、真实路径名

部署

```xml
<web-app ...>
	<servlet>
		<servlet-name>Internal name 1</servlet-name>
		<servlet-class>foo.Servlet1</servlet-class>
	</servlet>
  	<servlet>
		<servlet-name>Internal name 2</servlet-name>
		<servlet-class>foo.Servlet2</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>Internal name 1</servlet-name>
		<url-pattern>/Public1</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>Internal name 2</servlet-name>
		<url-pattern>/Public2</url-pattern>
	</servlet-mapping>
</web-app>
```

`<servlet-name>`元素用于把一个`<servlet>`绑定到一个特定的`<servlet-mapping>`元素，最终用户绝对看不到这个名，这个名只在这个部署描述文件的其他部分使用。

`<servlet-mapping>`，请求到来时，容器会在运行时使用这个容器询问*对于这个URL应该调用哪个Servlet*。

`<url-pattern>`是客户看到并使用的名称，可以使用通配符，是相对于上下文菜单`ContextPath()`的相对路径。

form中的`action="xxx.do"`是一个逻辑名而不是一个文件名，是客户使用的名字，在部署描述文件web.xml中会把客户请求资源`"xxx.do"`映射到一个实际的Servlet类文件。

#### Servlet3.0 注解

Servlet3.0 注解新特性免去web.xml配置，容器会根据web.xml中的metadata-complete元素的值来决定使用web.xml还是使用注解。如果该元素的值是true，那么容器不处理注解，web.xml是所有信息的来源。如果该元素不存在或者其值不为true，容器才会处理注解。

```java
@WebServlet(
		description = "Point to a JSP file", 
		urlPatterns = { "/ShowServlet" }, 
		initParams = { 
				@WebInitParam(name = "kehu", value = "kehuduan")
		})
```



### 1.4 简单的MVC过程

1 - The browser sends the request data to the Container.
2 - The Container finds the correct servlet based on the URL, and passes the request to the servlet.
3 - The servlet calls the BeerExpert for help.
4 - The expert class returns an answer, which the servlet adds to the request object.
5 - The servlet forwards the request to the JSP.
6 - The JSP gets the answer from the request object.
7 - The JSP generates a page for the Container.
8 - The Container returns the page to the happy user.

![简单的MVC过程](E:\于洪磊\文档\jiusuo\jiusuo\简单的MVC过程.png)

### 1.5 Servlet 生命周期

```sequence
Title:Servlet生命周期
容器-->Servlet类:加载类
容器-->Servlet对象:初始化Servlet（构造函数运行）
容器-->Servlet对象:init():servlet一生只调用一次
容器-->Servlet对象:service()：Servlet一生主要在这里度过
Note right of Servlet对象:doGet()、doPost()等
容器-->Servlet对象:destroy()：只能调用一次
```

## 2 会话和请求

### 2.1 监听器

![Listener](E:\于洪磊\文档\jiusuo\jiusuo\Listener.png)

注解

```
@WebListener 
@WebListener注解被应用在作为listener监听web应用程序事件的类上.
```



### 2.2 作用域

![属性作用域](E:\于洪磊\文档\jiusuo\jiusuo\属性作用域.png)

### 2.3 会话管理

HttpSession 接口  关键方法

```java
getAttribute();
setAttribute();
removeAttribute();
getCreationTime();//返回第一次创建会话的时间
getLastAccessedTime();//容器最后一个获得这个会话过去了多长时间(ms)
setMaxInactiveInterval();//指定对于这个会话客户请求的最大间隔(s)
getMaxInactiveInterval();
invalidate();//结束会话
```

