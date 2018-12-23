# Servlet 技术
参考资料：J2EE课件

### 简介
Servlet用于扩展请求 - 响应编程模型的服务器功能。 尽管servlet可以响应任何类型的请求，但它们通常用于扩展Web服务器的应用程序。 对于此类应用程序，Java Servlet技术定义了专用于HTTP的servlet类。

### Java Web 应用请求处理过程
![示意图](https://s1.ax1x.com/2018/12/11/FJO0YQ.png)

* 客户端向服务器发送一个HTTP请求
* 实现了Servlet和JSP技术的服务器将请求转换为一个HTTPServletRequest对象
* 对象被传入一个能和JavaBean组件或数据库交互以获取动态内容的Web组件
* Web组件可以生成一个HTTPServletResponse对象或者传递给其他组件，最终有一个组件生成HTTPServletResponse对象
* Web服务器转换为一个HTTP响应并返回给客户端

### Servlet对应的Java包
* javax.servlet.Servlet接口，定义：
	所有servlet类必需的一些公共方法 
* javax.servlet.ServletConfig接口：
	包含容器生成的Servlet配置信息 
* javax.servlet.GenericServlet抽象类：
	实现了Servlet接口和ServletConfig接口需要的许多方法；
	service()方法由具体的子类实现
* javax.servlet.http.HttpServlet类：
	init(ServletConfig config)方法
	service(HttpServletRequest requ, HttpServletResponse resp)方法
	destroy()方法

### 生命周期 Life Cycle
servlet的生命周期由部署servlet的容器控制。 当请求被映射到servlet时，容器执行以下步骤：
1. 如果servlet的实例不存在，则为Web容器
    1. 加载servlet类。
    2. 创建servlet类的实例。
    3. 通过调用init方法初始化servlet实例。
2. 调用服务方法，传递请求和响应对象。 
3. 如果容器需要删除servlet，则通过调用servlet的destroy方法来完成。

#### 创建Servlet
使用@WebServlet注解在Web应用程序中定义servlet组件。使用@WebServlet注解的类必须扩展javax.servlet.http.HttpServlet类。带注解的servlet必须至少指定一个URL模式（使用urlPatterns或value属性）。

#### 初始化Servlet
覆盖Servlet接口的init方法或指定@WebServlet注解的initParams属性，以自定义初始化过程。 无法完成初始化过程的servlet应该抛出UnavailableException。

#### 编写服务方法
servlet提供的服务是在GenericServlet的方法中实现的，在doMethod方法（Get，Delete，Options，Post，Put或Trace）中，或者在任何其他实现Servlet接口协议特定方法中实现。

方法的一般模式是从请求中提取信息，访问外部资源，然后根据信息填充响应。

#### 填充响应
1. 从响应中检索输出流。
2. 填写响应标头。
3. 将任何正文内容写入输出流。

#### 终止Servlet
当容器确定删除servlet时（例如，当容器想要回收内存资源或需要关闭它时），容器会调用Servlet接口的destroy方法。 在此方法中，释放servlet正在使用的任何资源并保存任何持久状态。

### 从Request获取信息
HttpServletRequest中包含了请求URL、HTTP头信息、查询字符串……

#### HTTP request URL
http://[host]:[port]\[request-path\]?\[query-string\]

* getContextPath - 上下文路径：正斜杠（/）与servlet Web应用程序的上下文根的合称。
* getServletPath - Servlet路径：与响应此请求的组件别名对应的路径部分。 此路径以正斜杠（/）开头。
* getPathInfo - 路径信息：请求路径中不属于上下文路径或servlet路径的部分。

#### 查询字符串
查询字符串由一组参数和值组成。 使用getParameter方法从请求中检索单个参数。

* 查询字符串可以显式出现在网页中。
* 提交带有GET HTTP方法的表单时，会将查询字符串附加到URL。

### 构建响应
HttpServletResponse，包括状态码和cookie信息

### Web.xml
* 标题：DOCTYPE声明，告诉服务器适用的servlet规范的版本，指定DTD
> <!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">

* 主正文：根元素web-app
> <web-app>
> ……
> </web-app>

#### \<servlet\>
```xml
<servlet>
	<servlet-name>Hello</servlet-name>
	<servlet-class>servlet.HelloServlet</servlet-class>
</servlet>
```
servlet-name的含义：
* 首先，初始化参数、定制的URL模式以及其他定制通过此注册名而不是类名引用此servlet
* 其次,可在URL而不是类名中使用此名称

#### \<servlet-mapping\>
指定Servlet可以映射到哪种URL模式
```xml
<servlet-mapping>
	<servlet-name>Hello</servlet-name>
	<url-pattern>/</url-pattern>
</servlet-mapping>
```
* / 匹配到/login这样的路径型url，不会匹配到模式为\*.jsp这样的后缀型url /\*
* /\* 匹配所有url：路径型的和后缀型的url(包括/login,\*.jsp,\*.js和\*.html等)

### 单线程和多线程：
servlet默认是多线程的，Server创建一个实例，用它处理并发请求——编写线程安全的类，避免使用可以修改的类变量和实例变量。修改servlet类，实现SingleThreadMode1接口——单线程。

### Session
会话由HttpSession对象（javax.servlet.http.HttpSession）表示。 可以通过调用请求对象的getSession方法来访问会话。 此方法返回与此请求关联的当前会话，或者，如果请求没有会话，则会创建一个会话。
```java
HttpSession session=request.getSession(false);
//如果会话对象不存在，不能创建新会话，否则如果不断提交请求，一个人可以创建大量的会话对象，会耗尽服务器上的内存资源。
if(session==null){
    response.sendRedirect(“http://……/login”);
    //强迫用户登录，成功登录之后，才能创建新会话
    //当用户注销时，使会话失效--session.invalidate()
} 
```
session按名称将对象值属性与会话关联。
```java
session.setAttribute(name,attribute); // 使用指定的名字把对象捆绑到会话中
session.getAttribute(name); // 返回捆绑到会话中指定名字上的对象
session.removeAttribute(name)：// 删除绑定
```
* 跟踪用户的购物车
* 导航信息，登录状态

### cookie
```java
Cookie myCookie=new Cookie(name,value);
resp.addCookie(myCookie); //加到HTTP响应信息中
myCookie.setMaxAge(int expiry); //浏览器将把cookie存储在本地计算机的硬盘上
Cookie[] cookies=req.getCookies(); //获取浏览器发送的cookie
```
* 用户登录ID
* 用户对语言和颜色的选择之类的偏好
* 跟踪应用程序的使用情况

### 过滤器 Filter
过滤器是一个对象，它可以转换请求或响应的标头和内容(或两者)。过滤器不同于web组件，本身不会创建响应，不应该对其他web资源有任何依赖关系。因此，它可以与其他类型的web资源组合在一起。
![过滤器图示](https://s1.ax1x.com/2018/12/20/FDXrqS.png)

过滤器可以执行的主要任务如下：
* 查询请求并采取相应措施。
* 阻止请求和响应的进一步传递。
* 修改请求标头和数据。
* 修改响应标头和数据。

例如：身份验证，日志记录，图像转换，数据压缩，加密，标记化流，XML转换等。

#### 过滤器实现
javax.servlet 包中的Filter, FilterChain和 FilterConfig 接口。使用@WebFilter("URL pattern")定义过滤器及相应的路径匹配模式。也可以在其部署描述符中指定过滤器映射列表。

javax.servlet.Filter接口：
```java
public void init(FilterConfig config) throws ServletException; 
public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException; 
public void destroy( );
```
* 最重要的方法是doFilter，它传递请求，响应和过滤器链对象，完成主要的过滤工作。
* 当实例化过滤器时，容器会调用init方法，则可以将初始化参数从FilterConfig对象中传入。

#### 过滤器使用
在调用servlet之前，截获请求，验证用户身份，未经授权的用户遭到拒绝。
```java
@WebFilter("servlet名")
public class OnlyDsFilter implements Filter{
	...
	// 将请求转发给过滤器链上的下一个对象
	// 下一个filter 或 请求的资源
	chain.doFilter(request, response);
}
```
如果有多个filter与当前请求（URL-Pattern）匹配，按照web.xml中filter-mapping出现的顺序运行。

### 监听器 Listener
使用监听器监控servlet生命周期事件并作出响应。Listener类通过实现listener接口来监听事件。主要作用有：监听客户端的请求、服务端的操作来自动激发一些操作

![servlet事件列表](https://s1.ax1x.com/2018/12/23/Fy24PO.png)

#### 监听器实现
@WebListener 定义监听器，等价于
```xml
<listener>
  <listener-class>edu.nju.login.listeners.CounterListener</listener-class>
</listener>
```

#### 监听器使用
* 监听Session、request、context的创建与销毁：HttpSessionLister、ServletContextListener、ServletRequestListener
* 监听对象属性变化，分别为：HttpSessionAttributeLister、ServletContextAttributeListener、ServletRequestAttributeListener 
* 监听Session内的对象，分别为HttpSessionBindingListener 和 HttpSessionActivationListener（不需要在web.xml里配置）
