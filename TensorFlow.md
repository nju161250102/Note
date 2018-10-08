# TensorFlow 学习

基于斯坦福tensorflow课程：[课程地址](https://web.stanford.edu/class/cs20si/syllabus.html)

### Overview
一些使用TensorFlow的开源项目：  
* 皮肤癌分类
* 原始音频深度生成
* 绘画
* 风格迁移    

学习目标：  
* 理解TF的计算图方法
* 了解TF内建的函数和类
* 学习如何构造深度学习项目最合适的模型

基础知识：  
* 张量Tensor：N维数组，0维-标量，1维-向量，2维-矩阵……
* 节点Node：运算符、变量、常量；边Side：张量
* 在session中计算并获取张量的值：session将检查并计算图中所有需要的节点，使用with从句
* 会话Session：提供运算对象被执行、张量对象被计算的环境，保存变量的当前值。session.run的参数是要计算的张量对象(列表)
* 子图Subgraphs：将计算图分布在不同的设备上执行
* 图Graph：with g1.as_default_graph(): / g = tf.get_default_graph(). 保存计算、分解计算、便于分布式计算、常见的机器学习模型已经按照图来学习。

### Operations
使用TensorBoard进行可视化展示：   
```
writer = tf.summary.FileWriter('./graphs', tf.get_default_graph())
或 writer = tf.summary.FileWriter('./graphs', sess.graph)
运行tensorboard --logdir="./graphs"
```
#### 常量
tf.constant( value, dtype=None, shape=None, name='Const', verify_shape=False)  

**使用特殊值填充：**  
全为0：  
tf.zeros(shape, dtype=tf.float32, name=None)  
tf.zeros_like(input_tensor, dtype=None, name=None, optimize=True)  
全为1：  
tf.ones(shape, dtype=tf.float32, name=None)  
tf.ones_like(input_tensor, dtype=None, name=None, optimize=True)  
全为固定值：tf.fill(dims, value, name=None)   
填充为序列：  
tf.lin_space(start, stop, num, name=None)   
tf.range(start, limit=None, delta=1, dtype=None, name='range')  
随机数填充：  
tf.random_normal  
tf.truncated_normal  
tf.random_uniform  
tf.random_shuffle  
tf.random_crop  
tf.multinomial  
tf.random_gamma  

**运算/操作：**  
|数据类型|示例|
|:--|:--|
|元素运算|Add,Sub,Mul,Div,Exp,Log,Greater,Less,Equal|
|数组运算|Concar,Slice,Split,Constant,Rank,Shape,Shuffle|
|矩阵运算|MarMul,MarrixInverse,MurixDeterminanr|
|静态操作|Variable,Assign,AssignAdd|
|神经网络构造块|SoftMax,Signmid,ReLU,MaxPool|
|检查点操作|Save,Check|
|算术操作|abs,negative,sign,reciprocal,square,round,sqrt,rsqrt,pow,exp|

**tensorflow与numpy的对比**  
* 基础数据类型（如int32）是相同的。
* tf.Session.run(fecthes)传入tensor，返回的是numpy.ndarray

**Tips**  
* 常量被存储在图的定义部分：图被存储在协议缓冲区
* 常量很大时图的加载将很耗费资源：对基本类型使用常量，使用读取器获取内存的更多数据  

#### 变量
tf.Variables() / tf.get_variable()，变量使用前必须初始化：sess.run(tf.global_variables_initializer())。  
eval(): 返回变量的值  
assign(): 在session内部生效，sess.run(assign_op)  
每一个session拥有唯一的一份变量副本。  
依赖控制：tf.Graph.control_dependencies(control_inputs)，控制operation执行的先后顺序。  

#### Placeholder
tf.placeholder(dtype, shape=None, name=None)shape用于检查输入值的规格是否符合要求。  
图一开始并不知道需要被用于计算的值，我们可以在计算执行的时候再提供。使用一个字典提供值：{x(Tensor, not String): value}  

#### 懒加载
一次性加载成百上千的数据会使图表臃肿，加载缓慢，解决方案：  
* 在计算/运行操作中单独定义操作
* 使用Python特性确保函数在第一次调用时也被加载

