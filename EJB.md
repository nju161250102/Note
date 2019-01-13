# EJB (Enterprise Java Bean )

### 简介
使用Java语言编写，封装应用程序业务逻辑（满足应用程序目的的代码）的服务器端组件。

### 优点
* EJB容器为企业Bean提供系统级服务（例如事务管理和安全授权），开发人员专注于解决业务问题
* 客户端人员专注于界面开发，不必编写业务规则的实现和访问数据库的代码
* 企业Bean是可移植组件，可以在任何兼容的Java EE服务器上运行

### 应用场景
* 应用程序是可扩展的，需要在多台计算机上运行
* 事务必须确保数据完整性。
* 应用程序需要多种客户端

### 会话Bean
会话bean封装了可以由客户端通过本地，远程或Web服务客户端视图以编程方式调用的业务逻辑。要访问部署在服务器上的应用程序，客户端将调用会话Bean的方法，在服务器内执行业务。会话Bean不是持久的，数据不会保存到数据库中。

- @Remote：表明Bean可以被远程客户端获取
- Serializable：钝化时可以被序列化

#### 有状态会话Bean
* 对象的状态由其实例变量的值组成
* 会话bean不共享，只有一个对应客户端
* 状态在会话期间保存，除非被客户端删除

应用场景：
* bean的状态表示bean与特定客户端之间的交互
* bean需要跨方法调用保存有关客户端的数据
* 在客户端与其他组件之间为客户端提供简化视图
* 管理几个bean的工作流程

![FjVae1.png](https://s2.ax1x.com/2019/01/12/FjVae1.png)

* @Stateful：有状态会话Bean
* @PostConstruct：依赖注入之后执行
* @PreDestroy：被销毁之前执行
* @PrePassivate：被钝化之前执行
* @PostActivate：被激活之后执行

**钝化与激活**

为了限制会话Bean示例在内存中的数量，EJB容器把Bean实例交换出去（钝化），如果客户端调用时Bean示例处于钝化状态，则激活，易产生I/O瓶颈。钝化时保存成员变量、EJB对象引用、JNDI命名上下文，释放数据库连接、打开的套接字、打开的文件等没用必要保存的资源，序列化保存入磁盘。

#### 无状态会话Bean
客户端调用无状态bean的方法时，bean的实例变量可能包含特定于该客户端的状态，但仅在调用期间。 方法完成后，不应保留特定于客户端的状态。无状态会话bean可以实现Web服务，但有状态会话bean不能。

应用场景：
* 状态中没有特定客户端的数据
* 为所有客户端执行通用任务
* 实现一个web服务

无状态Session Bean池：管理Bean的生命周期

![FjV1iT.png](https://s2.ax1x.com/2019/01/12/FjV1iT.png)

* @Stateless：表明这是一个无状态会话Bean
* @PostConstruct：依赖注入之后执行
* @PreDestroy：被销毁之前执行

#### 单例会话Bean
实例化一次，并且存在于应用程序的生命周期中，用于客户端共享和同时访问单个企业bean实例的情况。应用程序启动时应该实例化单例，应用程序关闭时执行清理任务，因为单例将在应用程序的整个生命周期中运行。

分布式环境中没有绝对的单例，集群部署时应该属于同一个Application，引入单例容易导致并发问题。

![FjZN9S.png](https://s2.ax1x.com/2019/01/12/FjZN9S.png)

* @Singleton：表明这是单例会话Bean

### 消息驱动Bean
允许Java EE应用程序异步处理消息。它通常充当JMS消息侦听器，也可以处理其他类型的消息。发送者和接收者之间是松耦合关联，只需要知道发送消息的格式。

**使用场景：**
* 会话bean发送JMS消息并同步接收它们，要异步接收消息，使用消息驱动的bean

#### 消息风格
- P2P消息风格：按照发送的顺序把消息写入并保存到队列，消费者处理队列中的消息。
- Publish/Subscribe消息风格：消息发送给一个主题，每个主题将消息发送给每个订阅者。

#### 消息消费方式
- 同步：订户或接收方通过调用receive方法显式从目标中获取消息。 接收方法可以阻止，直到消息到达，或者如果消息未在指定的时间限制内到达，则可以超时。
- 异步：客户端可以向消费者注册消息监听器。 消息侦听器类似于事件侦听器。 每当消息到达目的地时，JMS提供者通过调用侦听器的onMessage方法来传递消息，该方法对消息的内容起作用。

### 无界面视图
无界面视图将企业bean实现类的公共方法暴露给客户端，业务接口是标准的Java编程语言接口，包含企业bean的业务方法。

### 依赖注入
通过依赖注入，使用Java注解或JNDI查找获取对企业bean实例的引用。在Java EE服务器管理的环境之外运行的应用程序（例如Java SE应用程序）必须执行显式查找。JNDI支持用于标识Java EE组件的全局语法，以简化此显式查找

### 客户端程序
* 执行JNDI检索，找到Bean业务接口的引用
`EJBFactory.getEJB("ejb:/application/beanName!stateless_package_name")`
`EJBFactory.getEJB("ejb:/application/beanName!stateful_package_name?stateful")`
* 利用引用，调用业务方法

### JMS
允许应用程序创建、发送、接收、读取消息
![FvaUSg.png](https://s2.ax1x.com/2019/01/13/FvaUSg.png)

- JMS Provider：一个实现JMS接口并提供管理和控制功能的消息传递系统
- JMS Client：是用Java编程语言编写的用于生成和使用消息的程序或组件
- Message：是在JMS客户端之间传递信息的对象
- Administered objects：目标和连接工厂

#### JMS 连接工厂
本地客户端
```java
//以JBoss6.0为例
@Resource(mappedName=“ConnectionFactory")
QueueConnectionFactory factory;
//以JBoss7及以上为例
@Resource(mappedName=“java:/ConnectionFactory“)
QueueConnectionFactory factory;
```
远程客户端
```java
//以JBoss6.0为例
setProperty()设置Properties
//以JBoss7及以上为例
put()设置Properties

ctx = new InitialContext(props);
ConnectionFactory factory=(ConnectionFactory) ctx.lookup("ConnectionFactory");
```
#### JMS 目的地
本地客户端：
```java
//以JBoss6.0为例
@Resource(mappedName="queue/QueueName")
Queue queue;
//以JBoss7及以上为例
@Resource(mappedName=“java:jboss/exported/jms/queue/test“)
Queue queue;
```
远程客户端
```java
//以JBoss6.0为例
Queue queue=(Queue) ctx.lookup("queue/QueueName");
//以JBoss7及以上为例
Queue queue=(Queue) ctx.lookup(“jms/queue/QueueName");
```

#### JMS Connections
```java
Connection connection = connectionFactory.createConnection();
connection.close();
```

#### JMS Message Producer
```java
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(queue/topic);
producer.send(message);
```

#### JMS Message Consumer
```java
MessageConsumer consumer = session.createConsumer(queue/topic);
connection.start();
//blocks indefinitely until a message arrives:
Message m = consumer.receive();
//time out after a second
Message m = consumer.receive(1000);
```
同步消费者：

- receive()方法：如果有可用的消息，返回这个消息，否则将一直等待
- receiveNoWait()方法：如果有可用的消息，返回这个消息，否则返回NULL 
- receive(long timeout)方法：根据给定的超时参数制定的时间等待一个消息的到来，如果在这个时间之内有可用的消息，返回这个消息，如果超时后仍没有可用的消息，返回NULL

异步消费者：  
实现MessageListener接口，该接口包含一个onMessage方法。`consumer.setMessageListener(myListener);` 如果应用程序需要过滤收到的消息，可以使用JMS API消息选择器。

#### JMS Message
包括三部分：  
* Message Headers（必须）：Destination、ID、Type、setter&getter
* Message Properties
* Message Bodies：TextMessage, MapMessage, BytesMessage, StreamMessage, ObjectMessage

```java
TextMessage message = context.createTextMessage(); //JMS2.0
message.setText(msg_text); // msg_text is a String
producer.send(dest,message); //JMS2.0
```

#### JMSContext (JMS 2.0)
JMSContext对象将连接和会话组合在一个对象中。 
`JMSContext context = connectionFactory.createContext();`
如果没有来自应用程序或客户端的参数，或者没有活动的JTA事务，createContext方法将创建一个非事务会话。如果正在进行JTA事务时，createContext方法将创建事务会话。

- 创建Producer：`JMSProducer producer = context.createProducer();`
- 创建Consumer：`JMSConsumer consumer = context.createConsumer(dest);`