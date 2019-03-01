# Java 基础知识

### 1. 数据类型

| 数据类型  | 包装类 | 位数 | 范围 |
| ---- | ---- | ---- | ---- |
| byte | Byte | 8 | -128~127 |
| short | Short | 16 | -32768~32767 |
| int | Integer | 32 | …… |
| long | Long | 64 | …… |
| float | Float | 32 | 3.4e-38~3.4e+38 |
| double | Double | 64 | 1.7e-308~1.7e+308 |
| char | Character | 16 | \u0000~\uffff |
| boolean | Boolean | ~ | true/false |

注意点：

- boolean没有规定具体的大小，JVM 并不支持 boolean 数组
- 进制：16进制0x开头，八进制0开头，二进制0b开头
- 类型标识：long以L结尾，double以d结尾，float以f结尾
- 分隔符：便于读数可使用_分隔数字，不能用于开头和结尾（忽略标识符）

#### 缓存池

- new Integer(123) 每次都会新建一个对象；
- Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。

自动装箱过程中会调用valueOf方法，因此值相同时可能会引用相同的对象。

### 2. String类型

String 被声明为 final，因此它不可被继承。Java8中 String 内部使用 char 数组存储数据；Java9中使用 byte 数组存储字符串，同时使用 coder 来标识使用了哪种编码。value 数组被声明为 final，这意味着 value 数组初始化之后就不能再引用其它数组。并且 String 内部没有改变 value 数组的方法，因此可以保证 String 不可变。

#### 字符串常量池

```java
String a = "aaa";  // 以字面量形式创建时自动放入常量池
String b = "aaa";  // 相同的常量共享内存块，获得相同的引用
String c = "aa" + "a";  // 虚拟机优化：将常量的连接结果视作常量
String d = new String("aaa");  // 变量，存放于堆空间之中
String e = "a";
String f = e + "aa";  // 变量与常量的连接结果为变量
String g = d.intern();  // 如果已经存在一个字符串和该字符串值相等（equals），返回其中字符串的引用；否则，就会在常量池中添加一个新的字符串，并返回这个新字符串的引用。
```

#### String & StringBuilder & StringBuffer

**1. 可变性**

- String 不可变

- StringBuffer 和 StringBuilder 可变

  ```java
  StringBuilder builder = new StringBuilder();
  builder.append("123").append("456");  // 无需接收返回值
  // StringBuilder中含有很多字符串处理方法
  ```

**2. 线程安全**

- String 不可变，因此是线程安全的
- StringBuilder 不是线程安全的
- StringBuffer 是线程安全的，内部使用 synchronized 进行同步

### 3. 运算符

#### i++和++i的区别

i++指运算完后给i加1，++i指先给i加1再计算表达式的值。

i++和++i都不是原子操作。

#### 位运算符

| 操作符 | 名称       | 描述                                                    |
| ------ | ---------- | ------------------------------------------------------- |
| ＆     | 与         | 如果相对应位都是 1，则结果为 1，否则为 0                |
| \|     | 或         | 如果相对应位都是 0，则结果为 0，否则为 1                |
| ^      | 异或       | 如果相对应位值相同，则结果为 0，否则为 1                |
| 〜     | 非         | 按位取反运算符翻转操作数的每一位，即 0 变成 1，1 变成 0 |
| <<     | 左移       | 左移一位乘2                                             |
| >>     | 有符号右移 | 右移一位除2                                             |
| >>>    | 无符号右移 | 以零填充空位                                            |

位运算使用技巧：

```java
// 交换两个变量的值
a = a ^ b;
b = a ^ b;
a = a ^ b;

// 计算a+b
int add(int a, int b) {
    int c = a & b;
    int r = a ^ b;  // 得到不含进位之和
    if(c == 0) return r;
    else return add(r, c << 1);  // 进位
}
```

#### 类型转换

Java 不能隐式执行向下转型，因为这会使得精度降低。

```java
// float a = 1.1;
float a = 1.1f;
short b = 1;
// b = b + 1;
b += 1;  // 或者b++，等价于 b = (short) (b + 1);
```

### 4. 流程控制

- 注意短路运算符 (&&, ||)
- switch 可以比较String
- 使用goto和label跳出多重循环

### 5. 封装

#### 参数传递

Java 的参数是以值传递的形式传入方法中，而不是引用传递。

#### 访问控制

| 访问控制符 | 同类 | 同包 | 子类 | 不同包 |
| ---------- | ---- | ---- | ---- | ------ |
| public     | 能   | 能   | 能   | 能     |
| protected  | 能   | 能   | 能   | 不能   |
| default    | 能   | 能   | 不能 | 不能   |
| private    | 能   | 不能 | 不能 | 不能   |

信息隐藏（封装）：隐藏了方法的实现细节，外界只能与其通过API交互

#### static关键字

主要使用场景：

1. 修饰成员变量和成员方法(建议通过类名访问)

   - 被static修饰的成员变量属于静态成员变量，存放在 Java 内存区域的方法区
   - 被static修饰的方法是静态方法，只能访问静态成员变量和静态方法。不能是抽象方法，不能有 this 和 super 关键字

2. 静态代码块

   定义在类的方法外部，在非静态代码块之前执行(静态代码块—非静态代码块—构造方法)，按出现顺序执行并且只执行一次。用于项目启动必须执行的代码。

3. 修饰类(只能修饰内部类)

   非静态内部类在编译完成之后会保存着一个指向外部类的引用，而静态内部类没有。因此它的创建是不需要依赖外围类的创建，也不能使用任何外围类的非static成员变量和方法。

   ```java
   // Example（静态内部类实现单例模式）
   public class Singleton {
       
       // 声明为 private 避免调用默认构造方法创建对象
       private Singleton() {
       }
       
       // 声明为 private 表明静态内部该类只能在该 Singleton 类中被访问
       private static class SingletonHolder {
           private static final Singleton INSTANCE = new Singleton();
       }
   
       public static Singleton getUniqueInstance() {
           return SingletonHolder.INSTANCE;
       }
   }
   ```

4. 静态导包(用来导入类中的静态资源，1.5后的新特性)

   导入某个类中的指定静态资源，并且不需要使用类名调用类中静态成员，可以直接使用类中静态成员变量和成员方法。

#### 构造函数

构造函数没有返回类型，默认构造函数不带参数，可以使用 super() 函数访问父类的构造函数

#### 析构函数

finalize 方法：当虚拟机回收类时自动调用

### 6. 继承与接口

#### abstract & interface

抽象类与抽象方法：抽象类不能实例化，需要继承抽象类才能实例化其子类；抽象方法没有方法体。

接口：接口的成员（字段 + 方法）默认且必须都是 public 的；接口的字段默认都是 static 和 final 的。

抽象类与接口的区别

1. 接口的方法默认是 public，所有方法在接口中不能有实现(Java 8 开始可以有默认实现），抽象类可以有非抽象的方法
2. 接口中的实例变量默认是 final 类型的，而抽象类中则不一定
3. 一个类可以实现多个接口，但最多只能实现一个抽象类
4. 一个类实现接口的话要实现接口的所有方法，而抽象类不一定
5. 接口不能用 new 实例化，但可以声明，但是必须引用一个实现该接口的对象 
6. 从设计层面来说，抽象是对类的抽象，是一种模板设计，接口是行为的抽象，是一种行为的规范。

#### 覆盖/重写 Override

子类中的方法名和参数列表均与父类相同，则称子类覆盖了父类的方法。可以通过使用 super 关键字来引用父类的方法实现。

- 子类方法不能缩小父类方法的访问权限
- 子类方法不能抛出比父类方法更多的异常
- 子类方法的返回类型必须是父类方法返回类型或为其子类型

#### final关键字

- 类：不能被继承
- 方法：不能被覆盖（ private 方法隐式地被指定为 final ）
- 成员变量：赋初始值后不能被修改
  - 对于基本类型，final 使数值不变
  - 对于引用类型，final 使引用不变，也就不能引用其它对象，但是被引用的对象本身是可以修改的

#### Object 共同的基类

```java
// 计算对象的散列值，等价的两个对象散列值一定相同，但是散列值相同的两个对象不一定等价。
public native int hashCode()
// 判断对象是否等价
public boolean equals(Object obj)
// 必须显式重写才能使用
// 如果一个类没有实现 Cloneable 接口又调用了 clone() 方法，就会抛出 CloneNotSupportedException。
protected native Object clone() throws CloneNotSupportedException
// @ 后面的数值为散列码的无符号十六进制表示
public String toString()

public final native Class<?> getClass()

protected void finalize() throws Throwable {}

public final native void notify()

public final native void notifyAll()

public final native void wait(long timeout) throws InterruptedException

public final void wait(long timeout, int nanos) throws InterruptedException

public final void wait() throws InterruptedException
```

#### 拷贝

- 浅拷贝：拷贝对象和原始对象的引用类型引用同一个对象
- 深拷贝：拷贝对象和原始对象的引用类型引用不同对象。

使用 clone() 方法来拷贝一个对象即复杂又有风险，它会抛出异常，并且还需要类型转换。因此最好不要去使用 clone()，可以使用拷贝构造函数或者拷贝工厂来拷贝一个对象。

### 7. 多态

#### 方法重载 Overload

定义多个同名的方法，但是具有不同的参数类型、个数或顺序，由编译器检查并决定使用哪个方法。

涉及到重写时，方法调用的优先级为：

- this.show(O)
- super.show(O)
- this.show((super)O)
- super.show((super)O)

### 8. 异常处理

Throwable 可以用来表示任何可以作为异常抛出的类，分为两种： **Error**  和 **Exception**。

- Error ： JVM 无法处理的错误
- Exception：可以处理的异常
  - RuntimeException：不强制要求必须捕获，但是一旦发生程序将崩溃且无法恢复

#### try...catch...finally

不论发生什么异常，finally从句中的代码一定会执行，除非之前通过 System.exit(0) 终止程序

finally从句中应该主动回收内存：

- 关闭不再使用的数据库连接对象
- 关闭不再进行读写操作的IO对象
- 调用 clear 方法清空不再使用的容器集合
- 将不再使用的对象引用指向 null

#### throw & throws

- throws 声明方法会抛出异常
- throw 在方法体内部抛出异常

### 9. 反射

#### 查看属性与方法

```java
// 获取所有属性
Feild[] feilds = myClass.getDeclaredFields();
// getModifiers()	修饰符
// getGenericType()	类型
// getName()		名称

// 获取所有方法
Method[] methods = myClass.getDeclaredMethods();
// 获取所有构造函数
Constructor[] cs = myClass.getDeclaredConstructors()
```

#### 加载类

```java
// 返回Class类型的对象，此时会执行静态代码块中的内容
Class<> clazz = Class.forName("Myclass");
// 把指定的类加载到虚拟机内存中
MyClass myClass = (MyClass)clazz.newInstance();
```

#### 调用类的方法

```java
// 传递方法名和所有参数类型
Method method = clazz.getMethod(methodName, class);
// 指定调用的对象和传递的参数
Object invoke = method.invoke(object, param)
```

### 10. 泛型

泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。泛型的类型替换在编译阶段完成，只在编译阶段有效。

#### 泛型类与泛型方法

```java
// 定义一个泛型类
public class ExampleClass<T> {
    private T key;
    
    public setKey(T key) {
        this.key = key;
    }
    
    public T getKey() { // getKey的返回值类型为T
        return this.key;
    }
	……
}
```

#### 泛型接口

```java
// 定义一个泛型接口
public interface ExampleInterface<T> {
    public T next();
}
```

实现泛型接口的类，未传入泛型实参时，与泛型类的定义相同，在声明类的时候，需将泛型的声明也一起加到类中。

```java
// 泛型接口中为<T>
class ExampleImpl<T> implements ExampleInterface<T>{
    @Override
    public T next() {
        return null;
    }
}
```

实现泛型接口的类，传入泛型实参时，所有使用泛型的地方都要替换成传入的实参类型。

```java
// 泛型接口中为<具体类>
public class FruitGenerator implements Generator<String> {
    @Override 
    public String next() { 
        return ""; 
    } 
}
```

#### 泛型方法

public与返回值之间的\<T>必不可少，这表明这是一个泛型方法，并且声明了泛型。只有声明了\<T>的方法才是泛型方法，泛型类中的使用了泛型的成员方法并不是泛型方法。

```java
public <T,K> K ExampleMethod(Class<T> class) {
    ……
}
```

#### 类型通配符

可以把？看成所有类型的父类。避免了子类无法被转换成父类