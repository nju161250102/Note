# AOP面向切面编程

### 1. 关键词

|名词||释义|
|:---|:---|:---|
|方面|Aspect|横切部分的常见功能|
|接合点|Join-point|在接合点上执行“方面”|
|通知|Advice|由方面所执行的行为|
|切入点|Point-cut|一组接合点组成的表达式|
|目标|Target|执行流被方面更改的对象|
|编织|Weaving|编译、加载或运行时将方面与目标对象相结合的过程|

### 2. 通知的类型
|类型|接口|执行点|
|:---|:---|:---|
|Before|MethodBeforeAdvice|在接合点之前执行通知|
|After Returning|MethodReturningAdvice|在接合点执行完成之后执行通知|
|After Throwing|ThrowsAdvice|如果结合点抛出异常则执行通知|
|After(Finally)|N/A|接合点执行完毕之后，不管是否抛出异常都执行通知|
|Around|N/A|在接合点周围执行通知|

### 3. 切入点指示符
* 类型签名表达式 Within(<type name>)
* 方法签名表达式 Execute(<scope> <返回值><完全限定类名>*(参数表))

|通配符|定义|
|:---|:---|
|..|匹配方法中任何数量的参数，匹配类定义中任何数量的包|
|+|匹配给定类的任何子类|
|*|匹配任何数量的字符|

### 4. AOP注解
|注解|功能|
|:---|:---|
|@Before|在实际方法调用前调用被注解的通知方法|
|@PointCut|用于复用的切入点表达式，通知上可以使用逻辑运算符连接多个切入点|
|@After|实际方法成功返回或是抛出异常都会调用|
|@AfterReturning|实际方法成功返回后执行|
|@AfterThrowing|实际方法抛出异常后执行|
|@Aspect|Bean类级别上应用的注解，此外应使用一个@Component|
|@Around|目标方法执行之前和之后启动通知方法执行|
|@DeclareParent|提供一个接口的具体类让目标对象动态实现接口|
|@EnableAspectJAutoProxy|开启AOP代理|

* 访问传递给实际方法的参数：args(param)
* 访问接合点返回值：returning = ""
* 拦截实际方法抛出的异常：throwing = ""





