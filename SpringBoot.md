# SpringBoot框架

## 1. application.properties配置
变量名采用驼峰写法或者全小写加-  
### 基础配置
* server.port=[端口号]8080  
* server.tomcat.uri-encoding=[URI字符编码]UTF-8  

### 数据库配置
* spring.datasource.url [数据库URL]  
    * jdbc:mysql://localhost:3306/name?characterEncoding=UTF-8  
    * jdbc:sqlserver://localhost:1433;database=name  
* spring.datasource.driverClassName
    * com.mysql.jdbc.Driver
    * com.microsoft.sqlserver.jdbc.SQLServerDriver
* spring.datasource.username=[用户名]
* spring.datasource.password=[密码]   

### DAO框架配置
#### mybatis
* mybatis.config-locations=[主配置文件地址]classpath:mybatis/mybatis-config.xml
* mybatis.mapper-locations=[映射关系文件地址]classpath:mybatis/mapper/*.xml
#### jpa-hibernate
* spring.jpa.database[数据库类型]  
[sql_server / mysql / ...]
* spring.jpa.show-sql[是否显示sql语句]  
[true / false]
* spring.jpa.hibernate.ddl-auto[同步策略]  
    * create 没有表格会新建表格，表内有数据会清空
    * create-drop 程序结束的时候会清空表
    * update 没有表格会新建表格，表内有数据不会清空，只会更新
    * validate 运校验数据与数据库的字段类型是否相同，不同会报错
* spring.jpa.hibernate.naming.physical-strategy[数据库表字段命名策略]
    * org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl 不作任何修改
    * org.springframework.boot.orm.jpa.hibernate.SpringPhysicalNamingStrategy 遇到大写字母改小写加"_"命名
