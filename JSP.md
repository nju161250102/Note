# JSP (JavaServer Pages)

### JSP技术简介
JSP技术提供了Java Servlet技术的所有动态功能，但提供了一种更自然的方法来创建静态内容。 主要特点如下：
* 基于文本的文档，描述如何处理请求和构造响应
* 用于访问服务器端对象的表达式语言
* 可以定义JSP语言扩展的机制

JSP页面包含：
* 静态数据：例如HTML，SVG，WML和XML
* 动态内容：JSP元素

```html
<!-- example -->
<html>
  <head>
    <title>Hello World</title>
  </head>
  <body>
    <%
      out.print("<p><b>Hello World!</b>");
    %>
  </body>
</html>
```

### JSP的生命周期
JSP页面将和servlet一样为请求提供服务，因此它的生命周期和其他特性由servlet技术决定。如果jsp对应的servlet要比jsp页面旧，web容器将会重新将jsp转化为servlet并且编译类。

页面翻译和编译后，JSP页面的servlet（大部分）遵循servlet生命周期：
1. 如果JSP页面的servlet实例不存在，则容器：
    a.  加载JSP页面的servlet类
    b.  实例化servlet类的实例
    c.  通过调用jspInit方法初始化servlet实例
2. 容器调用Service方法，传递请求和响应对象。

如果容器需要删除JSP页面的servlet，它会调用Destroy方法。

### JSP语法
![标准语法和XML语法](https://s1.ax1x.com/2018/12/22/FyZWN9.png)

### Directives
Directives指令用于控制Web容器如何转换和执行JSP页面。

#### session
<%@ page session= " true " %>
指定当前的JSP页面访问请求应包括在一个HTTP会话中，缺省选项" true "。相当于request.getSession(true)

#### import
<%@ page import= " package.* " %>
导入包中的所有类，下列包是隐含导入到JSP页面中的：
* java.lang.\*
* javax.servlet.\*
* javax.servlet.jsp.\*
* javax.servlet.http.\*

#### extends
<%@ page extends= " com.xxx.JspCommon " %>
扩展其他类

#### contentType
<%@ page contentType= "text/html,charset=GBK" %>
设置页面的文本类型和字符编码

#### buffer
<%@ page buffer="none|xxxkb" %> 
设置页面的缓冲区大小。
较大的缓冲区允许在将任何内容实际发送回客户端之前写入更多内容，从而为JSP页面提供更多时间来设置适当的状态代码和标题或转发到另一个Web资源。 较小的缓冲区会降低服务器内存负载，并允许客户端更快地开始接收数据。

#### errorPage
<％@ page errorPage =“errorpage.jsp”％>
指定Web容器应在发生异常时将控制转发到的错误页面。相当于在web.xml中设置：
```xml
<error-page>
	<exception-type>exceptionname</ exception-type >
	<location>/errorpagename</ location >
</error-page >
```

### Include
<%@ include file="filename" %>
是将包含在另一个文件（静态内容或另一个JSP页面）中的文本插入到包含JSP页面中。用于实现JSP页面的模块化，复用导航条、标题栏、页脚等。对应servlet的代码：
```java
RequestDispatcher dispatcher=getServletContext().getRequestDispatcher("/banner.jsp"); 
if(dispatcher!=null) dispatcher.include(request,response);
```

### Declarations
<%! scripting-language-declaration %>
用于在页面的脚本语言中声明变量和方法，将成为servlet中的声明。声明的变量只在当前页面中可用。在声明中可以自定义初始化jspInit()和资源释放jspDestory()的过程：
```jsp
<%!
	public void jspInit() {
		......
	}
%>
```

### Scriptlets
用于包含脚本语言的代码片段，例如当脚本语言设置为java时，scriptlet将转换为Java语句片段，并插入到JSP页面的servlet的service方法中。JSP页面可以访问在scriptlet中创建的变量。
```jsp
<%
	scripting-language-statements
%>
```

### Expression
计算表达式的值，转换成字符串并插入页面，不需要写分号。
<％= scripting-language-expression％>

### 隐式对象
隐式对象由Web容器创建。
#### out
* javax.servlet.jsp.JspWriter类的实例
* 调用print()/println()方法
* 作用域是当前页面（page）
* 采用缓存

#### request/response对象
* javax.servlet.HttpServletRequest/ HttpServletResponse的实例
* 使用request对象得到请求信息中的参数
* 使用response对象发送重定向、修改HTTP头、指定URL重写

#### session对象
* javax.servlet.http.HttpSession的实例
