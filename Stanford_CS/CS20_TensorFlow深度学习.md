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

----
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

----
### Basic Model
#### 线性回归模型
建立自变量x和因变量y之间的线性模型：  
* 模型函数：Y_predicted = w * X + b
* 损失函数：均方误差E[(y - y_predicted)^2]

**构建计算图**  
* 读取数据
* 为输入和标签创建占位符 tf.placeholder
* 创建权值和偏移量 tf.get_variable
* 推断/预测 Y_predicted = w * X + b
* 指定损失函数
```
loss = tf.square(Y - Y_predicted, name='loss')
```
* 创建优化器
```
opt = tf.train.GradientDescentOptimizer(learning_rate=0.001)
optimizer = opt.minimize(loss)
```

**训练模型**  
* 初始化变量
* 运行优化器（使用feed_dict将数据送入占位符）

**Huber loss**
邻域范围之外的损失小于均方误差。  
* 实现：tf.cond(pred, fn1, fn2, name=None)用于实现分段函数  
> tf控制流：流程控制、比较操作、逻辑操作、调试  

#### 数据集 tf.data 
placeholder：将数据置于tensorflow流程之外，在python中易于执行，在单线程中形成性能瓶颈减慢执行速度。  
dataset：直接使用数据而不是使用占位符之后再填入。  
```
tf.data.Dataset.from_tensor_slices((features, labels))
# features包含原始输入特征的字典或DataFrame
tf.data.Dataset.from_generator(gen, output_types, output_shapes)
# 从文件输入
tf.data.TextLineDataset(filenames)
tf.data.FixedLengthRecordDataset(filenames)
tf.data.TFRecordDataset(filenames)
```
tf.data.Iterator：创建遍历数据集的迭代器指针
```
dataset.make_one_shot_iterator()  # 迭代一次，不需要初始化
dataset.make_initializable_iterator()  # 迭代多次，每次需要初始化
sess.run(iterator.initializer)  # 初始化
X, Y = iterator.get_next()  # 使用
```
对数据进行处理：  
* shuffle 从数据集随机选取
* repeat 将数据集重复n次
* batch 将连续n个元素堆叠成一个元素
* map 应用转换函数进行预处理  

注意：  
* 对于原型设计，feed dict可以更快更容易编写
* 复杂的预处理或多个数据源时，tf.data难以使用
* NLP数据通常是一个整数序列，tf.data的加速并不明显

#### Optimizer优化器
session寻找所有优化器依赖的可训练(Variable的trainble属性)的元素并更新它们。  
```
tf.train.GradientDescentOptimizer  # 梯度下降算法：学习率
tf.train.AdagradOptimizer  # Adagrad算法：学习率
tf.train.MomentumOptimizer
tf.train.AdamOptimizer
tf.train.FtrlOptimizer
tf.train.RMSPropOptimizer
```

#### Logistics回归模型
MNIST手写数字数据集tensorflow.examples.tutorials.mnist  
* 模型函数：Y_predicted = softmax(X * w + b)
* 损失函数：交叉熵损失-log(Y_predicted)

创建迭代器：iterator = tf.data.Iterator.from_structure，可以为训练集和测试集分别创建迭代器。
其余步骤与线性回归模型类似……

---
### Eager Execution
命令式编程环境，立即评估操作，操作返回具体的值。

#### 图的优点与缺点
* 可优化：自动重用缓存、并行操作、自动平衡计算与存储性能
* 可部署：图是模型的中间表示
* 可重写
* 难以调试：构建后报告错误、不能即时输出状态
* 不python：控制流和python不同、难以和传统数据结构混合

#### Eager Execution的关键优点
* 使用Python调试工具
* 提供即时的错误报告
* 允许使用python的数据结构
* 可以使用简单、python化的控制流

#### 基本用法
不必使用占位符和session，可以立即返回计算结果。
> tf.Tensor 对象会引用具体值，而不是指向计算图中的节点的符号句柄。由于不需要构建稍后在会话中运行的计算图，因此使用 print() 或调试程序很容易检查结果。评估、输出和检查张量值不会中断计算梯度的流程。
> ——Tensor Flow中文教程

适合和numpy一起使用。  

#### 梯度
eager execution中内置了自动微分计算：所有前向传播操作都会记录到“磁带”中，"磁带"反向播放时计算梯度（反向传播算法） 
```
# 例1
grad = tfe.gradients_function(square)   # square是求平方函数
print(grad(3.))
# 例2
grad = tfe.implicit_gradients(loss)   # 使用tfe.Variable
def loss(y):
  return (y - x ** 2) ** 2
grad = tfe.implicit_gradients(loss)
```
---

### Manage Experiments
#### word2vec
最简单直接的想法：每个单词用一个只有一个1，其余为0的向量表示。问题：  
* 词汇量非常巨大：庞大的维数、低效率的计算
* 不能代表单词间的关系

理想的表示情况：  
* 分布式的描述
* 连续的值
* 低维度
* 捕获单词间的语义关系

用它相邻词汇的意思表示一个单词：简单但高效。  
Softmax和Negative Sampling（简化版噪声对比估计）  
> NCE loss:在分类数量太大时简化softmax计算（转化为二分类的问题），采用估算的方法。
> tf.nn.nce_loss

#### 重用模型
为你的模型定义一个类，使用函数创建模型的各个部分。

#### 命名空间
tensorflow不知道哪些节点应该被聚集在一起，除非手动指定：with tf.name_scope(name_of_that_scope):  
解决变量间的复用问题：tf.get_variable(\<name\>,\<shape\>, \<initializer\>)，如果name相同则重用，否则根据initializer初始化。
```
with tf.variable_scope('two_layers') as scope:
	scope.reuse_variables()
```

#### 保存/读取
```
# 保存的是session，而不是图
saver = tf.train.Saver()
tf.train.Saver.save(sess, save_path, global_step=None...)
tf.train.Saver.restore(sess, save_path)
# 使用global step
global_step = tf.Variable(0, dtype=tf.int32, trainable=False, name='global_step')  # trainable训练过程忽略此变量
# check if there is checkpoint
ckpt = tf.train.get_checkpoint_state(os.path.dirname('checkpoints/checkpoint'))
# check if there is a valid checkpoint path
if ckpt and ckpt.model_checkpoint_path:
     saver.restore(sess, ckpt.model_checkpoint_path)
```

#### tf.summary
```
# 第一步：创建summaries
with tf.name_scope("summaries"):
    tf.summary.scalar("loss", self.loss)
    tf.summary.scalar("accuracy", self.accuracy)            
    tf.summary.histogram("histogram loss", self.loss)
    summary_op = tf.summary.merge_all()  # 合并使管理更容易
# 第二步：将summary_op送入session计算
oss_batch, _, summary = sess.run([loss, optimizer, summary_op])
# 第三步：将summaries写入文件
writer.add_summary(summary, global_step=step)
```

#### 随机控制
计算图和每个操作都有自己的随机数种子seed。

#### 自动微分
计算图使得利用连锁规则计算梯度变得简单，自动实现前向传播算法。  
grad_z = tf.gradients(z, [x, y])  

----
### 计算机视觉与卷积神经网络介绍
#### 计算机视觉方向
* 对象分割
* 姿态识别
* 图片描述
* 密集图像标注
* 视觉问题回答
* 图片分辨率提升
* 艺术风格

#### 卷积神经网络
**卷积层**：源像素——卷积核——目标像素，过滤器总是扩展成和输入一样的深度。输入层经过过滤器会变成深度为1的一层激活层，通过使用不同的过滤器生成多层激活层。将激活层看作具有深度的输入可以重复以上过程。通常情况下，转换的步长为1，对于F\*F的过滤器，使用(F-1)/2的零填充。图片尺寸缩小太快是不明智的。

两个关键的观点：  
* 特征是分层的：使用低复杂度特征组合检测高复杂度特征更加有效。
* 特征是转换上不变的  

tf.layers.conv2d：  
* inputs输入：输入tensor
* filters过滤器：输出的深度（过滤器的个数）
* kernel_size卷积核大小
* strides步长
* padding边缘选项：VALID=无边缘，SAME=0填充  

**池化层**：使表示更小更易管理。  
**最后一层**： 最近邻：特征空间中L2最近邻。

----
### 风格转换
#### TFRecord
TensorFlow内部的推荐格式。  
* 利用磁盘缓存
* 快速移动
* 将不同格式的文件放在一起

#### 转换为TFRecord
```
# Step 1: 创建一个Writer
writer = tf.python_io.TFRecordWriter(out_file)
# Step 2: 获取图像的形状和值
shape, binary_image = get_image_binary(image_file)
# Step 3: 创建一个tf特征对象
features = tf.train.Features(feature={'label': _int64_feature(label),
                                    'shape': _bytes_feature(shape),
                                    'image':_bytes_feature(binary_image)})
# Step 4: 创建一个简单的包含上面特征的样本
sample = tf.train.Example(features=features)
# Step 5: 写入
writer.write(sample.SerializeToString())
writer.close()
```

#### 读取TFRecord
```
dataset = tf.data.TFRecordDataset(tfrecord_files)
dataset = dataset.map(_parse_function)

def _parse_function(tfrecord_serialized):
    features={'label': tf.FixedLenFeature([], tf.int64),
              'shape': tf.FixedLenFeature([], tf.string),
              'image': tf.FixedLenFeature([], tf.string)}

    parsed_features = tf.parse_single_example(tfrecord_serialized, features)

    return parsed_features['label'], parsed_features['shape'], parsed_features['image']
```

#### 风格迁移
寻找一副新的图像，使得内容与给出内容的图像最接近，风格与给出风格的图像更接近。  
* 低层次抽取内容上的特征
* 高层次抽取风格上的特征