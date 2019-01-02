# MVC 架构

### 概述

使用MVC架构的原因：

* 大多数复杂应用需要使用几种不同的方式查看和操作数据
* 当数据操作逻辑、格式化和显示代码同用户事件处理混杂在一起的时候，应用维护变得非常困难
* 应用逻辑同界面的代码混杂，用户界面无法复用
* 增加功能时难以对现有的代码进行修改
* 对单独一段代码进行修改会造成难以预料的后果
* web应用具有复杂的交互模型
* web应用需要国际化

Model View Controller（模型视图控制器）

把多个组件集成到一起，相互合作，协调一致的进行工作
* 模型：封装应用数据（关系数据库或EJB），处理商业逻辑
* 视图：呈现给用户的界面（JSP或应用GUI）
* 控制器：接受用户动作，并对应用数据进行适当的处理（Servlet）

使用MVC架构的优点：
* 通过控制器将视图与模型分离
* 同时开发包括模型，视图和控制器的各个组件。
* 能够对变化做出反应：
    * 在不影响业务逻辑的情况下更改用户界面
    * 在不影响UI的情况下更改业务逻辑
    * 在不影响UI或业务逻辑的情况下移动数据位置

### 以Servlet为例
![在servlet中使用MVC](https://s1.ax1x.com/2018/12/29/FfhC5Q.jpg)

* 由一个servlet应答初始的请求；
* Servlet完成实际的数据处理并将结果存储在bean中；
* Bean存储在HttpServletRequest, HttpSession, 或ServletContext中；
* Servlet使用RequestDispatcher的forward方法将请求转发到JSP页面；
* JSP页面通过使用jsp:useBean和相应的作用域（request, session, 或application ）从bean中读出数据。

注意：
* jsp页面不能创建对象，使用`<jsp:useBean type="package.Class" />`
* jsp页面不能修改对象属性，只能使用jsp:getProperty方法获取


