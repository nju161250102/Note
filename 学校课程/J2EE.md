# J2EE

### Web应用程序
Web应用程序是Web或应用程序服务器的动态扩展。
* 面向展示：面向表示的Web应用程序生成包含各种类型的标记语言（HTML，XHTML，XML等）和动态内容以响应请求的交互式Web页面。
* 面向服务：面向服务的Web应用程序实现Web服务的端点。

Web应用程序的组成：Web组件，静态资源文件（如图像和css），辅助类和库。

开发Web应用：
* 开发Web组件代码。
* 如有需要，编写Web应用程序部署描述文件。
* 编译组件引用的Web应用程序组件和帮助类。
* （可选）将应用程序打包到可部署的单元中。
* 将应用程序部署到Web容器中。
* 访问引用Web应用程序的URL。

### Web组件
Web组件为Web服务器提供动态扩展功能。 Web组件可以是Java servlet，使用JavaServer Faces技术实现的Web页面，Web服务端点或JSP页面。
> JavaServer Faces（JSF）是一个为网络应用程序构建基于组件的用户界面的Java规范，也是一个MVC Web应用框架，通过在页面中使用可重用的UI组件简化了基于服务器的应用程序的用户界面。
> 
> Servlet最适合面向服务和面向表示的应用程序的控制功能；JavaServer Faces和Facelets页面更适合生成基于文本的标记，例如XHTML。

### Web容器
Web容器提供诸如请求分派，安全性，并发性和生命周期管理之类的服务，还为Web组件提供对命名，事务和电子邮件等API的访问。

配置保存在称为Web应用程序部署描述符（DD）的XML文件中，也可以使用注解。

### Web模块
Web模块是最小的可部署和可用单元。 Web模块包含Web组件和Web资源、服务器端工具类（数据库Beans）、客户端类（applets）。 Java EE Web模块对应于Java Servlet规范中定义的Web应用程序。Web模块可以以未打包的文件结构部署，也可以打包成WAR部署。

Web模块的结构：
* Document Root/ web应用的顶层目录
  * WEB-INF/ 
    * lib/ jar库文件
    * classes/ class文件
    * tags/ （可选）tag库文件
    * web.xml 部署描述文件：指定安全信息；覆盖web组件信息
  * Web Pages 静态资源

