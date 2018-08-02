# hibernate框架

### 1. 数据库配置文件hibernate.cfg.xml
```
<hibernate-configuration>
    <session-factory>
        <!-- 数据库配置信息 -->
        <property name="dialect">数据库方言</property>
        <property name="connection.driver_class">数据库驱动</property>
        <property name="connection.url">数据库地址</property>
        <property name="connection.username">用户名</property>
        <property name="connection.password">登录密码</property>
        <!-- 数据库其他配置 -->
        <property name="show_sql">是否显示sql语句</property>
        <property name="format_sql">是否格式化sql语句</property>
        <property name="connection.autocommit">是否自动提交</property>
        <!-- 导入持久类配置文件 -->
        <mapping resource="employee.hbm.xml"/>  
    </session-factory>
</hibernate-configuration>
```
数据库方言：
* MySQL: org.hibernate.dialect.MySQL5Dialect
* SQL Server 2008: org.hibernate.dialect.SQLServer2008Dialect

### 2. 映射关系配置文件*.hbm.xml
```
<hibernate-mapping>  
  <class name="类的完全限定名" table="表名">  
    <!-- 主键标识 -->
    <id name="属性名">  
        <generator class="主键生成策略"></generator>  
    </id>  
    <property name="属性名" colmun="列名" type="数据类型"></property> 
  </class>  
 </hibernate-mapping>
```
主键生成策略配置
* assigned: 主键由外部程序负责生成
* increment: 取出主键的最大值并加1
* identity: 由数据库实现自增长
* native: 由数据库自行判断
* uuid: 随机生成32位不相同的字符串


