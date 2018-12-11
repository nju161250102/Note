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