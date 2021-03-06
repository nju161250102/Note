# Java 并发

并发编程的优点：

- 利用多核CPU的计算性能
- 适应复杂的业务模型拆分

并发编程的缺点：

- 频繁的上下文切换（解决：无锁并发编程、CAS算法，最少的线程，使用协程）
- 线程不安全或者死锁

### 基础概念

- 同步调用：调用者必须等待被调用的方法结束后，才能执行后面的代码
- 异步调用：调用者不用管被调用方法是否完成，都会继续执行后面的代码
- 并发：多个任务交替进行
- 并行：多个CPU同时进行
- 阻塞：等待占有临界区资源的线程释放资源
- 临界区：多个线程之间的共享数据

#### 线程的状态与操作

![](https://cyc2018.github.io/CS-Notes/pics/96706658-b3f8-4f32-8eb3-dcb7fc8d5381.jpg)

新建线程：

- 继承 Thread 类，重写run方法
- 实现 runable 接口（由于Java不支持多继承，而且继承整个类开销过大，因此尽量使用此方法）
- 实现 callable 接口（返回的是异步执行的结果）

运行转为等待：

- sleep：静态方法，让当前线程按指定时间休眠；sleep方法只让出CPU，不会失去锁；休眠时间结束后获得时间片即可执行
- wait：实例方法；wait方法会释放占有的对象锁；等待notify 或 notifyAll 
- notify：唤醒，恢复正常运行的状态

### 基础操作

#### Executor

Executor 管理多个异步任务的执行，而无需程序员显式地管理线程的生命周期。这里的异步是指多个任务的执行互不干扰，不需要进行同步操作。

- CachedThreadPool：一个任务创建一个线程；
- FixedThreadPool：所有任务只能使用固定大小的线程；
- SingleThreadExecutor：相当于大小为 1 的 FixedThreadPool。

#### 守护线程

守护线程是程序运行时在后台提供服务的线程，不属于程序中不可或缺的部分。当所有非守护线程结束时，程序也就终止，同时会杀死所有守护线程。例如垃圾回收线程属于守护线程，main() 属于非守护线程。可以使用 setDaemon() 方法将一个线程设置为守护线程，要先于start()方法。

#### yield

静态方法，使当前线程让出CPU，但是如果在下一次竞争中，又获得了CPU时间片当前线程依然会继续运行。另外，让出的时间片只会分配**给当前线程相同优先级**的线程。可以通过**setPriority(int)**方法进行设置，但是不同的JVM和操作系统会有差异。

#### 中断

通过调用一个线程的 interrupt() 来中断该线程，如果该线程处于阻塞、限期等待或者无限期等待状态，那么就会抛出 InterruptedException，从而提前结束该线程。但是不能中断 I/O 阻塞和 synchronized 锁阻塞。当抛出InterruptedException时候，会清除中断标志位。

### Java 内存模型特性

- 原子性：单个内存操作具有原子性，但是组合来看并不具备。可以使用原子类，也可以使用 synchronized 互斥锁来保证操作的原子性
- 可见性：当一个线程修改了共享变量的值，其它线程能够立即得知这个修改。
  - volatile
  - synchronized，对一个变量执行 unlock 操作之前，必须把变量值同步回主内存。
  - final，被 final 关键字修饰的字段在构造器中一旦初始化完成，并且没有发生 this 逃逸（其它线程通过 this 引用访问到初始化了一半的对象），那么其它线程就能看见 final 字段的值。
- 有序性：本线程内观察，所有操作都是有序的。在一个线程观察另一个线程，所有操作都是无序的。volatile 关键字通过添加内存屏障的方式来禁止指令重排。也可以通过 synchronized 来保证有序性，它保证每个时刻只有一个线程执行同步代码，相当于是让线程顺序执行同步代码。

#### 先行发生原则

- 单一线程原则：在一个线程内，在程序前面的操作先行发生于后面的操作。
- 管程锁定原则：一个 unlock 操作先行发生于后面对同一个锁的 lock 操作。
- volatile 变量规则：对一个volatile 变量的写操作先行发生于后面对这个变量的读操作。
- 线程启动原则：Thread 对象的 start() 方法调用先行发生于此线程的每一个动作。
- 线程加入原则：Thread 对象的结束先行发生于 join() 方法返回。
- 线程中断规则：对线程 interrupt() 方法的调用先行发生于被中断线程的代码检测到中断事件的发生。
- 对象终结规则：一个对象的初始化完成（构造函数执行结束）先行发生于它的 finalize() 方法的开始。
- 传递性

### 线程安全

#### 不可变

不可变的类型：

- final 关键字修饰的基本数据类型
- String
- 枚举类型
- Number 部分子类，如 Long 和 Double 等数值包装类型
- 使用 `Collections.unmodifiableXXX()` 方法来获取一个不可变的集合

#### 互斥同步

**synchronized**

可以用在方法上也可以使用在代码块中，其中方法是实例方法和静态方法分别锁的是该类的实例对象和该类的对象。如果锁的是类对象的话，尽管new多个实例对象，但他们仍然是属于同一个类依然会被锁住，即线程之间保证同步关系。

- **悲观锁策略**：假设每一次获取锁时都会发生冲突，所以当前线程会阻塞其他线程获取锁
- **乐观锁策略**：假设所有线程访问共享资源的时候不会出现冲突，使用CAS来鉴别是否出现冲突

**ReentrantLock**

支持重入性，表示能够对共享资源能够重复加锁，即当前线程获取该锁再次获取不会被阻塞。

- 重入性：获取锁时如果被占有，检查占有线程是否是当前线程
- 公平锁：锁的获取顺序就应该符合请求上的绝对时间顺序，满足FIFO
- 非公平锁：刚释放锁的线程可能再次获取到锁

公平锁保证请求资源时间上的绝对顺序，非公平锁有可能导致其他线程永远无法获取到锁，造成“饥饿”现象

公平锁为了保证时间上的绝对顺序，需要频繁的上下文切换，而非公平锁会降低一定的上下文切换，降低性能开销，保证了系统更大的吞吐量。

#### 非阻塞同步

- CAS：整数原子类 AtomicInteger 
- ABA：原子引用类 AtomicStampedReference 

#### 无同步方案

- 栈封闭：多个线程访问的是同一个方法的局部变量
- 线程本地存储：ThreadLocal 把共享数据的可见范围限制在同一个线程之内
- 可重入代码：可以在代码执行的任何时刻中断它，转而去执行另外一段代码（包括递归调用它本身）

### 锁优化

#### 自旋锁

互斥同步进入阻塞状态的开销很大，应该尽量避免。让一个线程在请求一个共享数据的锁时执行忙循环（自旋）一段时间，如果在这段时间内能获得锁，就可以避免进入阻塞状态。

#### 锁消除

对于被检测出不可能存在竞争的共享数据的锁进行消除

#### 锁粗化

对同一个对象反复加锁和解锁，会把加锁范围扩展到整个操作的外部

#### 锁的状态

- 无锁状态（unlocked）
- 偏向锁状态（biasble）偏向于让第一个获取锁对象的线程，这个线程在之后获取该锁就不再需要进行同步操作，甚至连 CAS 操作也不再需要
- 轻量级锁状态（lightweight locked）使用 CAS 操作来避免重量级锁使用互斥量的开销
- 重量级锁状态（inflated）