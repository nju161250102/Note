# Java Persistence API
持久化：对象序列化、对象/关系型数据库映射、对象数据库

### 实体Entities
轻量级持久域对象，通常每个实体对应一张表，每个实例对应表中的一行

#### 实体类
- 使用注解`javax.persistence.Entity annotation`
- public或protected的无参构造函数
- 类、成员变量、方法不能被声明为final
- 实现`Serializable`接口
- 持久化实例变量必须声明为private，通过方法访问状态

#### 持久字段与持久属性
- 实例变量 —— 持久字段，运行时（通过反射）直接访问实体类实例变量。 所有未@Transient的字段将持久保存到数据存储中，必须使用对象/关系映射注释。
- JavaBeans样式属性的getter方法 —— 持久属性，必须遵循JavaBeans组件的方法约定，必须使用对象/关系映射注释。

#### 主键
每个实体的唯一对象标识符，必须拥有。 简单主键使用javax.persistence.Id标注。 复合主键使用javax.persistence.EmbeddedId和javax.persistence.IdClass注解表示。

#### 示例
```java
@Entity
@Table(name="users")
public class User implements Serializable {
	@Id
	private String id;
	private double account;
	public User() {
	}
	// getter and setter
}
// 不为持久字段和属性指定@Column注释，将假定到同名字段和属性的数据库列的默认映射
```

### 管理实体类
实体管理器由javax.persistence.EntityManager实例表示。 每个EntityManager实例都与持久性上下文（存在于特定数据存储中的一组托管实体实例）相关联。
对于容器管理的实体管理器，持久性上下文由容器自动注入：
```java
@PersistenceContext
EntityManager em;
```
对于应用程序管理的实体管理器，持久性上下文不会传播到应用程序组件。
```java
@PersistenceUnit
EntityManagerFactory emf;
EntityManager em = emf.createEntityManager();
```

#### 查找操作
使用主键查找数据：`User user = em.find(User.class, userID)`

#### 查询操作
* executeUpdate：批量查询
* getSingleResult：获取单个结果集，如果没有获取到数据，则会抛出NoResultException异常。如果获取多条数据的话，则会抛出NonUniqueResultException异常
* getResultList：获取对应的结果集合，指定顺序的集合，需要使用List作为返回值类型。如果没有获取到数据的话，则返回一个空集合，不会抛出异常

#### 管理实例的生命周期
实体的实例处于以下四种状态：

- New：没有持久化也不与持久化内容关联，通过JVM获得了一块内存空间，但数据库中没有数据与之对应
- Managed：具有持久化也与持久化内容关联，任何对于该实体的更新都会同步到数据库中
- Detached：游离状态，不处于持久化上下文中，任何修改都不会同步到数据库中
- Removed：删除状态，计划从数据库中删除

![生命周期.png](https://s2.ax1x.com/2019/01/15/Fztuyd.png)

#### 持久化单元配置
persistence.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
 <persistence version="2.0" xmlns="http://java.sun.com/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/persistence http://java.sun.com/xml/ns/persistence/persistence_2_0.xsd">
    <!--必须要有name属性，不能为空 -->
    <persistence-unit name="jpaPU" transaction-type="RESOURCE_LOCAL">
        <provider>org.hibernate.ejb.HibernatePersistence</provider>
        <jta-data-source>java:/DefaultDS</jta-data-source>
        <jar-file>MyApp.jar</jar-file>
        <class>org.acme.Employee</class>
        
        <properties>
             <!--配置Hibernate方言 -->
             <property name="hibernate.dialect" value="org.hibernate.dialect.MySQL5Dialect" />
             <!--配置数据库驱动 -->
             <property name="hibernate.connection.driver_class" value="com.mysql.jdbc.Driver" />
             <!--配置数据库用户名 -->
             <property name="hibernate.connection.username" value="root" />
             <!--配置数据库密码 -->
             <property name="hibernate.connection.password" value="root" />
             <!--配置数据库url -->
             <property name="hibernate.connection.url" value="jdbc:mysql://localhost:3306/jpa?useUnicode=true&amp;characterEncoding=UTF-8" />
             <!--设置外连接抓取树的最大深度 -->
             <property name="hibernate.max_fetch_depth" value="3" />
             <!--自动输出schema创建DDL语句 -->
             <property name="hibernate.hbm2ddl.auto" value="update" />   
          </properties>
     </persistence-unit>
 </persistence>
```

### Java Persistence 查询语言
- SELECT从句定义查询的返回类型，也可以返回统计函数的结果
- FROM从句定义查询范围
- WHERE从句作为状态表达式限制了查询返回的对象
- DISTINCT消除重复值
- GROUP BY和HAVING从句
- ORDER BY从句
- `position = :position`实体持久字段 = :`Query.setNamedParameter`设置的参数
- 可以使用原生SQL查询`Query createNativeQuery(String sql)`

#### 数据库表关系
* 一对一关系：使用OneToOne注解
* 一对多关系：使用OneToMany注解
* 多对一关系：使用ManyToOne注解
* 多对多关系：使用ManyTomany注解

#### 级联删除
使用@OneToOne和@OneToMany关系的cascade = REMOVE元素指定级联删除关系。cascade的值可选择`CascadeType.PERSIST`(级联新建)、 `CascadeType.REMOVE`(级联删除)、` CascadeType.REFRESH`(级联刷新)、` CascadeType.MERGE`(级联更新)中的一个或多个，`CascadeType.ALL`表示选择全部选项