# SpringBoot框架

## 1. application.properties配置
变量名采用驼峰写法或者全小写加-，以下未统一格式  
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

## 2. Spring 常用注解
#### @SpringBootApplication:  
包含@Configuration、@EnableAutoConfiguration、@ComponentScan，通常用在主类上。
#### @Repository:  
用于标注数据访问组件，即DAO组件。
#### @Service:  
用于标注业务层组件。 
#### @Controllelr:  
用于标注控制层组件(如struts中的action)
#### @RestController:  
包含@Controller和@ResponseBody。
#### @ResponseBody：  
表示该方法的返回结果直接写入HTTP response body中，一般在异步获取数据时使用。  
#### @Configuration：
指出该类是Bean配置的信息源。
#### @AutoWired:
把配置好的Bean拿来用，完成属性、方法的组装，它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。加上required=false时，就算找不到bean也不报错。  
#### @RequestMapping：
处理请求地址映射，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。
#### @RequestParam：  
用在方法的参数前面。  
#### @PathVariable:  
路径变量。 

## 3. Spring Data JPA
### Repository接口
#### Repository 
仅仅是一个标识，表明任何继承它的均为仓库接口类，方便Spring自动扫描识别  
#### CrudRepository<S实体类, ID主键数据类型>
提供的接口：  
```
<S extends T> S save(S entity);
<S extends T> Iterable<S> save(Iterable<S> entities);
T findOne(ID id);
boolean exists(ID id);
Iterable<T> findAll();
Iterable<T> findAll(Iterable<ID> ids);
long count();
void delete(ID id);
void delete(T entity);
void delete(Iterable<? extends T> entities);
void deleteAll();
```
可以直接使用Repository提供的一些方法,但是必须方法的命名要遵守一定的规则,例如findByXx,findByXxLike...  
使用@Query注解来解决定义规则的麻烦和实现复杂查询，查询语言是hibernate使用的hql  
#### PagingAndSortingRepository  
继承CrudRepository，实现了一组分页排序相关的方法。 
```
Iterable<T> findAll(Sort sort);//升序或者降序的查询所有
Page<T> findAll(Pageable pageable);//分页查询,传入Pageable对象,返回一个Page对象
```
#### JpaRepository  
继承PagingAndSortingRepository，实现一组JPA规范相关的方法。  
执行分页排序的查询时,可以附加查询条件。  

## Spring Data REST
