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