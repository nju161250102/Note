# TensorFlow实战

## 3 TensorFlow入门

### 3.1 计算模型——计算图
每一个计算是计算图上的一个节点，每一条边描述了计算之间的依赖关系。  
系统会自动维护一个默认的计算图，可以通过tf.get_default_graph来获取。  
不同计算图上的计算和张量不会共享。  
tf.Graph.device指定运行计算的设备。  
通过集合collection管理不同类别的资源。  

### 3.2 数据模型——张量
所有的数据都通过张量来表示，可以被简单理解为多维数组。  
张量的三个属性：  
* 名字：node:src_output，节点的名称:张量的输出序号 
* 维度：
* 类型：张量的所有输入类型必须一致，主要类型有浮点、整型、布尔、复数，类型不可改变  

张量的主要用途：  
* 获取中间结果  
* 保存计算结果  

### 3.3 运行模型——会话
会话session管理tensorflow运行时所有的资源，通过python的上下文管理器进行：  
```
with tf.Session() as sess:
    sess.run()
```
直接构建默认会话：tf.InteractiveSession()  
配置会话：tf.ConfigProto()，配置并行线程数、GPU分配策略、日志记录  

### 3.4 实现神经网络
#### 3.4.1 神经网络简介
神经网络解决分类问题的步骤：  
* 提取实体中的特征向量作为神经网络的输入。
* 定义神经网络的结构，即如何从输入得到输出。  
* 通过训练数据调整神经网络中参数的取值。
* 使用训练好的神经网络进行预测。  

#### 3.4.3 神经网络参数与TensorFlow变量
tf.Variable() 变量的声明与初始化  
tf.random_normal() 正态分布随机数  
tf.random_uniform() 平均分布随机数  
tf.zeros/fill/ones() 以重复数字填充  
tf.constant() 产生一个给定值的常量  
tf.initialize_all_variables() 初始化所有变量  
tf.all_variables() 拿到当前计算图上所有的变量  

#### 3.4.4 训练神经网络模型
定义placeholder作为存放输入数据的地方，维度可以由推导而来。

#### 3.4.5 完整的神经网络模型
```
# coding=utf-8
import tensorflow as tf
import numpy as np

w1 = tf.Variable(tf.random_normal([2, 3], stddev=1, seed=1))
w2 = tf.Variable(tf.random_normal([3, 1], stddev=1, seed=1))

x = tf.placeholder(tf.float32, [None, 2], 'x-input')
y_ = tf.placeholder(tf.float32, [None, 1], 'y-input')

a = tf.matmul(x, w1)
y = tf.matmul(a, w2)

cross_entropy = -tf.reduce_mean(y_ * tf.log(tf.clip_by_value(y, 1e-10, 1.0)))
train_step = tf.train.AdamOptimizer(0.001).minimize(cross_entropy)

dataset_size = 128
X = np.random.random((dataset_size, 2))
Y = [[int(x1 + x2 < 1)] for x1, x2 in X]

batch_size = 8
with tf.Session() as sess:
    init_op = tf.global_variables_initializer()
    sess.run(init_op)
    for i in range(5000):
        start = (i * batch_size) % dataset_size
        end = min(start + batch_size, dataset_size)
        sess.run(train_step, {x: X[start:end], y_: Y[start:end]})
    print(sess.run(w1))
    print(sess.run(w2))
```
步骤：  
* 定义神经网络的结构和前向传播的输出结果  
* 定义损失优化以及选择反向传播优化的算法  
* 生成会话并反复运行优化算法

## 4 深层神经网络
进一步介绍如何涉及和优化神经网络，使它能够更好地对未知样本做出预测。  

### 4.1 深度学习与深层神经网络
#### 4.1.1 线性模型的局限性
线性模型的输出为输入的加权和。由于任意线性模型的组合还是线性模型，因此多层的神经网络（使用线性激活函数）和单层没有区别。  
线性模型处理问题的能力是有局限的，无法解决线性不可分问题。  

#### 4.1.2 激活函数实现去线性化
常用非线性激活函数：ReLU、sigmoid、tanh。提供7种不同的非线性激活函数。

#### 4.1.3 多层网络解决异或运算
深度学习网络具有组合特征提取的功能。  

### 4.2 损失函数定义
刻画神经网络的效果和优化的目标。  
#### 4.2.1 经典损失函数
**分类问题**  
交叉熵：刻画了两个概率分布之间的距离。  
softmax：将神经网络的输出变成概率分布。  
> tf.clip_by_value(n, a, b) 确保n被限制在[a, b]范围之内  
> tf.log() 依次求对数   
> tf.matmul() 完成矩阵乘法  
> tf.nn.softmax_cross_entropy_with_logits(y, y_) 采用softmax回归的交叉熵损失函数，其中y为神经网络输出，y_为标准结果。  
**回归问题**
均方误差MSE：tf.reduce_mean()实现。  

#### 4.2.2 自定义损失函数
自定义损失函数  

### 4.3 神经网络优化算法
随机梯度下降算法
