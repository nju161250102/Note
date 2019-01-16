# hibernate框架

### API 介绍
- Configuration：定位并读取映射文件的位置
- SessionFactory：单个数据库映射关系经过编译后的内存镜像，线程安全
- ConnectionProvider：生成JDBC连接的工厂（连接池）
- Session：持久层管理器，单线程对象，隐藏了JDBC连接
- TransactionFactory：生成Transaction对象实例的工厂
- Transaction：指定原子操作单元范围的单线程对象
- Query和Criteria：对数据库及持久对象进行查询的接口
- Callback：允许用户程序能对一些事件的发生做出相应的操作

### 配置映射关系
* 编写映射文件来定义映射关系：\*.hbm.xml文件，位于WEB-INF/classes目录中
* 通过注解映射

#### 文件
```xml
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

#### 注解
* @Entity  
    实体类  
* @Table(name = "表名")  
    默认以类名作为表名  
* @Id  
    声明属性为主键  
* @GeneratedValue
    表示主键是自动生成策略，一般和@Id一起使用。  
    * AUTO 默认选项，由数据库自动生成   
    * IDENTITY 由自己指定  
* @Column(length=长度, nullable=是否允许为空, name="字段名")    
* @Lob  
    声明字段为 Clob 或 Blob 类型  
* @Temporal(value=TemporalType.DATE)  
    做日期类型转换  
    DATE（日期）, TIME（时间）, TIMESTAMP（日期+时间）  
* @Transient  
    不进行持久化

### 配置Hibernate
### xml文件
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
数据库方言：解决不同数据库之间的差异性  
* MySQL: org.hibernate.dialect.MySQL5Dialect
* SQL Server 2008: org.hibernate.dialect.SQLServer2008Dialect

#### 属性文件
属性文件名为hibernate.properties，内部包含键值对。
```txt
hibernate.connection.usename 用户名
hibernate.connection.password 密码
hibernate.connection.url JDBC连接字符串
hibernate.dialect Hibernate方言类
hibernate.connection.driver_class JDBC驱动类
```

### 代码方式
```java
Configuration configuration = new Configuration();
configuration.addResoure(“mapping.xml”).setProperty(“connection.username”,”root”);
……
```

### 数据库操作
1. 建立SessionFactory
2. 使用SessionFactory类的openSession方法来获得一个Session对象
3. 使用Session接口的beginTransation方法开始一个新事务
4. 使用Session接口的save方法保存实例
5. 如果成功，使用Transaction接口的commit方法提交事务，否则使用rollback方法回滚事务
6. 关闭Session对象

#### 建立会话工厂
```java
// Hibernate3
// 读hibernate.cfg.xml配置文件
Configuration config = new Configuration().configure();
// 建立SessionFactory
SessionFactory sessionFactory = config.buildSessionFactory();

// Hibernate4
// 将Hibernate的配置或者服务等统一向ServiceRegistry注册后，才能生效
ServiceRegistry serviceRegistry = new StandardServiceRegistryBuilder().applySettings(config.getProperties()).build();
SessionFactory sessionFactory = config.buildSessionFactory(serviceRegistry);

// Hibernate5
config.addAnnotatedClass(User.class);
```

#### 获得一个Session对象
```java
session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();
session.save(user); //保存Entity到数据库中
tx.commit();
session.close();
```
Session对象不是线程安全的，多个用户请求不能并发。`getCurrentSession()`创建的session会绑定到当前线程，会在事务回滚或事物提交后自动关闭。事务配置：本地事务`<property name="hibernate.current_session_context_class">thread</property>`；全局事务（JTA事务）`<property name="hibernate.current_session_context_class">jta</property>`
```java
public class HibernateUtil {
    private static final SessionFactory sessionFactory;
    public static SessionFactory getSessionFactory() {
        ......
    }
    public static Session getSession(){
        return getSessionFactory().getCurrentSession();
    }
}
```

#### HQL
面向对象的查询语言，查询以对象形式存在的数据，推荐的查询模式

### Hibernate缓存
* SessionFactory缓存（应用缓存），二级缓存：存放元数据和预定义SQL，被所有session共享
* Session缓存（事务缓存），一级缓存：提高访问性能，保证缓存对象与数据库同步，保证不出现对象的死锁，多线程之间不能共享

#### get与load
* get：首先在一级缓存中，通过实体类型和id进行查找。否则，依次查询二级缓存和数据库，没有找到返回null
* load：首先查session缓存，看id对应的对象是否存在。如果缓存中没有这个对象，则创建代理，使用对象时查询二级缓存和数据库，如果数据库没有这条数据，抛出异常ObjectNotFoundException
