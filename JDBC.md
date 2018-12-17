# JDBC
Java数据库连接 (Java DataBase Connectivity)

### JDBC的作用 
* 开发人员只需了解一组API，就可以访问任何关系数据库 
* 不必为不同的数据库重新编写代码 
* 不必了解数据库供应商的特定API 
* 几乎每个数据库供应商都具有几种类型的JDBC驱动

### JDBC的使用
1. 建立数据库连接：使用DriverManager机制或DataSource机制 
2. 向数据库提交查询请求
3. 读取查询结果
4. 处理查询结果
5. 释放连接

### DriverManager机制
使用DriverManager、Driver和Connection连接到数据库上
1. 注册驱动程序
```java
Class.forName(“JDBCDriverName”); //隐式注册
DriverManager.registerDriver (new JDBCDriverName()); //显式注册
```
2. 建立数据库连接
```java
Connection con=DriverManager.getConnection(URL,username,password); //按照注册顺序，找到第一个可以成功连接到给定URL的驱动程序，返回一个Connection对象
```
3. 使用连接进行查询、插入、删除的操作

DriverManager机制的弊端：
* 是一个同步的类，一次只有一个线程可以运行
* 与数据库相关的连接信息都包含在类中，如果用户更换另一台计算机作数据库服务器，就需要重新修改URL变量、重新编译、部署；
* 用户的用户名、口令也包含在类中，丧失了安全性

### DataSource机制
1. 查询数据源对象
```java
Context ctx=new InitialContext();
DataSource ods=(DataSource) ctx.lookup(DataSourceJNDIName); 
```
2. 获取数据库连接(javax.sql.DataSource类的工厂方法)
```java
ods.getConnection(); 
```
3. 进行数据库操作（与DriverManager机制相同）

### 数据库操作
```java
Statement stmt=con.createStatement(); //用于完成和数据库的交互
stmt.execute(“任何有效的SQL查询语句”); //执行SQL语句
ResultSet rs=stmt.getResultSet(); // 返回查询结果
//or
ResultSet rs=stmt.executeQuery(“SQL查询语句”);
```

#### ResultSet
拥有一个指向当前数据行的指针，开始，这个光标定位到**第一个数据行之前的位置。**
```java
next()
//把光标移动到下一行，当ResultSet对象没有更多的数据行时， next()方法返回false
getString(String ColumnName)
//读取Resultset对象当前行中指定列名的字符串值
```

#### 完成查询后
调用close()方法依次释放ResultSet对象，Statement对象，Connection对象。

### 事务
保证一系列数据库操作能够准确的完成，除非事务中的所有操作都成功，否则事务就不会完成。确保事务的四个特性：

* Atomicity（原子性）
* Consistency（一致性）
* Isolation（隔离性）
* Durability（持久性）

#### JDBC事务
自动提交 or 手动提交
```java
con.setAutoCommit(false); //关闭自动提交
con.commit(); //手动提交
```

#### JTA事务
1. 定位支持事务的数据源
DataSource ds= (DataSource) ctx.lookup(“MyDemoJDBCDataSource”); 
2. 建立数据库连接
javax.sql.Connection con= ds.getConnection();
3. 使用Connection对象，生成一个针对数据库的Statement对象，执行多个SQL语句
Statement stmt=con.createStatement();
4. 作为一个事务单位，这些SQL或者都成功的执行和提交，或者都回滚到数据库的初始状态
5. 关闭连接
stmt.close();
con.close();

#### 事务的隔离级