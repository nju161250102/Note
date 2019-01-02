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
RequestDispatcher dispatcher = getServletContext().getRequestDispatcher("/banner.jsp"); 
if(dispatcher != null) dispatcher.include(request,response);
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

### JSP 标准动作
#### <jsp:include page="">

使用RequestDispatcher在运行时把其他页面的内容加入输出流，而include指令是在编译时加入其他页面，被包含的资源可以是变量，例如`<jsp:include page="%=dynamicRef%">`

#### <jsp:forward page="">

把当前JSP请求转发到另一个资源上。HTTP重定向将请求信息发送回客户端，让客户端再进行资源转发，而forward标准动作只在服务器上完成。

#### <jsp:param name="" value="">

使用include或forward动作时，向指定页面传递附加参数，并只在当前作用域生效。

#### <jsp:plugin type="">

包含一个JavaBean或者applet

#### Bean动作
* <jsp:useBean id="beanName" class="完全限定类名" scope="scope">
  * JSP容器将自动创建/清理相应的JavaBean实例
* <jsp:setProperty name="beanName" porperty="">
  * beanName 必须和useBean中的id一样
  * JavaBean中必须有set方法
  * value填入字符串内容，param填入请求参数名（相同可不填，可使用通配符）
* <jsp:getProperty name="beanName" property=""> 和set方法类似 

### 自定义标签
自定义标记是用户定义的JSP语言元素，用于封装重复任务。 标记库定义了一组相关的自定义标记，并包含实现标记的对象。实现自定义标记的对象称为标记处理程序。

#### 语法
其中prefix是标识库的标记，tag是标记标识符，attr1 ... attrN是属性
```xml
<prefix:tag attr1="value"  ... attrN="">
  ...
</prefix:tag>
```

#### 使用
* 声明包含标签的标签库
  * `<%@ taglib prefix="tt" [tagdir=/WEB-INF/tags/dis|uri=URI] %>`
  * prefix属性定义了用于区分标签库的前缀
  * 多个文件定义的标签库使用tagdir属性（必须以/WEB-INF/tags开头）
  * uri属性特指使用标签库描述符TLD（扩展名为.tld）
```xml
<taglib>
    <tlib-verion>1.2</tlib-version>
    <jsp-version>2.0</jsp-version>
    <short-name>name</short-name>
    <tag>
        <name>tagName</name>
        <tag-class>处理标签的类</tag-class>
        <body-content>empty</body-content>
        <attribute>
            <name></name>
            <required>true</required>
            <rtexprvalue>true</rtexprvalue>
            <type>java.lang.String</type>
        </attribute>
    </tag>
</taglib>
```
* 使标记库实现可用于Web应用程序
实现SimpleTag接口或者继承SimpleTagSupport类，核心在于doTag方法

#### 优点
* 简单的jsp标签后隐藏着复杂的功能
* 复杂的功能封装在HTML风格的标签中
* 一定程度上实现了模块化
* JSP程序员实现基本功能，美工使用标签专注于数据的表达

### Unified EL
`${}`——右值表达式
`${object.name}`or`${objetc["name"]}`相当于get方法获取属性

值表达式可以引用下列对象及它们的属性：
* JavaBean组件
* 集合类
* Java SE枚举类型
* 隐含的对象

值表达式可以用于：
* 静态文本
* 任何可以接受表达式的标准或自定义标记属性

Web容器根据PageContext.findAttribute查找值来计算表达式之中的属性，查找范围是页面、请求、会话、应用程序，未找到则返回null

#### 函数
函数实现为公共类中的公共静态方法，然后将函数名称映射到TLD中的定义
```xml
<taglib>
    ……
    <function>
        <name>fun</name>
        <function-class>mypkg.fun</function-class>
        <function-signature>int fun(java.lang.String)</function-signature>
    </function>
</taglib>
```

### JSTL
JavaServer Pages标准标记库（JSTL）封装了许多JSP应用程序通用的核心功能。
JSTL具有诸如用于处理流控制的迭代器和条件，用于操作XML文档的标签，国际化标签，用于使用SQL访问数据库的标签以及常用功能的标签。

![FhSeUJ.png](https://s1.ax1x.com/2018/12/30/FhSeUJ.png)

#### 变量标签
* 设置变量`<c:set var="" scope="session" value="..."/>`
* 移除变量`<c:remove var="" scope="session">`

#### 条件标签
```jsp
<c:if test="">
</c:if>

<c:choose>
    <c:when test="">
        ...
    </c:when>
    <c:otherwise>
        ...
    </c:otherwise>
</c:choose>
```

#### 迭代器标签
循环内部使用var声明的变量名，类似Java中的for-each循环
```jsp
<c:forEach var="item" items="${sessionScope.cart.items}">
    ${item.quantity}
</c:forEach>
```

#### 其他标签
* \<c:import>：所有<jsp:include>标签具有的功能，允许包含绝对URL
* \<c:url>：将URL格式化为字符串，类似response.encode(URL)
* \<c:redirect>：重写URL跳转到新的URL，支持c:param标签
* \<c:catch>：用来处理产生错误的异常状况，并且将错误信息储存起来
* \<c:out>：用于在JSP中显示数据，就像<%= ... >
* xml标签：操作xml文档
* sql标签：与关系型数据库交互