---
layout: post
title: "Python（一）Tensorflow知识点归纳"
catalog: true
header-style: text
tag: 
    - Python
    - Tensorflow
---
## Session()

执行运算

### 与1.0的区别

2.0没有session函数，可以用以下代码：

```python
tf.compat.v1.disable_eager_execution()
with tf.compat.v1.Session() as sess:
    result=sess.run(xxx)
    sess.run()
    print(result)
```
或者：

```python
tf.compat.v1.disable_eager_execution()
result=tf.compat.v1.Session(xxx)
print(result)
tf.compat.v1.Session().close()
```
### Session()中的类型
* A `tf.Operation`.
      The corresponding fetched value will be `None`.
 * A `tf.Tensor`.
      The corresponding fetched value will be a numpy ndarray containing the value of that tensor.
  * A `tf.sparse.SparseTensor`.
      The corresponding fetched value will be a`tf.compat.v1.SparseTensorValue`containing the value of that sparse tensor.
   * A `get_tensor_handle` op.  The corresponding fetched value will be a numpy ndarray containing the handle of that tensor.
   * A `string` which is the name of a tensor or operation in the graph.

若为其他类型，需要先转换为其中一种类型



## Variable（）
变量都需要创建才能成为变量
### 变量的创建

```python
tf.Variable(initial_value)
```
### 变量的初始化
变量全部声明完成需要初始化，先将初始化变量设出

```python
init=tf.compat.v1.global_variables_initializer()
```
### 变量的改变
首先变量需要初始化

```python
 sess.run(init)
```
之后，变量没改变一次，都需要执行

```python
session.run()
```

获取变量当前值也需要

```python
print(session.run(变量名))
```



## placeholder()
### 用法
先预存变量，之后在session.run()的时候，将feed_dict字典输入对应元素值
### 示例

```python
tf.compat.v1.disable_eager_execution()

x=tf.compat.v1.placeholder(tf.float32)
y=tf.compat.v1.placeholder(tf.float32)
z=tf.add(x,y)

with tf.compat.v1.Session() as sess:
    print(sess.run(z, feed_dict={x: [2], y: [3]}))
```

## 创建神经网络
首先需要创建传递函数
代码是B站莫烦老师的代码，链接：[https://www.bilibili.com/video/BV1Lx411j7ws?p=16](https://www.bilibili.com/video/BV1Lx411j7ws?p=16)
```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt

tf.compat.v1.disable_eager_execution()
def add_layer(inputs,in_size,out_size,activation_function=None):
    '''

    :param inputs:输入神经元的值
    :param in_size: 输入神经元个数
    :param out_size: 输出个数
    :param activation_function:激励函数
    :return: 输出结果
    '''
    Weights=tf.Variable(tf.compat.v1.random_normal([in_size,out_size]))#设置权重
    biases=tf.Variable(tf.zeros([1,out_size])+0.1)#设置神经元的偏差值，需要非零
    Wx_plus_b=tf.matmul(inputs,Weights)+biases#未激活之前的神经元的值
    if activation_function==None:
        outputs=Wx_plus_b#默认是线性的
    else:
        outputs=activation_function(Wx_plus_b)#设置激活函数
    return outputs


x_data=np.linspace(-1,1,100)[:,np.newaxis]#给向量增加一个维度，张量由原来的（100）变为（100，1）

noise=np.random.normal(0,0.5,x_data.shape)#设置噪声
y_data=np.square(x_data)-0.5

#预设值xs、ys,[None,1]表示输入样本的数量可以是任意的
xs=tf.compat.v1.placeholder(tf.float32,[None,1])
ys=tf.compat.v1.placeholder(tf.float32,[None,1])

#隐藏层建立
l1=add_layer(xs,1,10,activation_function=tf.nn.relu)
#输出层结果预测
prediction=add_layer(l1,10,1,activation_function=None)
#代价函数
loss=tf.compat.v1.reduce_mean(tf.compat.v1.reduce_sum(tf.square(ys-prediction),reduction_indices=[1]))
#训练步骤
train_step=tf.compat.v1.train.GradientDescentOptimizer(0.1).minimize(loss)

#初始化变量
init=tf.compat.v1.global_variables_initializer()
sess=tf.compat.v1.Session()
sess.run(init)

#figure实例
fig=plt.figure()
#画子图，（1，1，1）表示子图共1行，共1列，此图的位置在第一个位置
ax=fig.add_subplot(1,1,1)
ax.scatter(x_data,y_data)
plt.ion()
plt.show()

for i in range(1000):
    sess.run(train_step,feed_dict={xs:x_data,ys:y_data})
    if i%50:
        ## 每隔一段时间输出代价值
        print(sess.run(loss, feed_dict={xs: x_data, ys: y_data}))
        try:
            ax.lines.remove(lines[0])
        except Exception:
            pass
		#画出拟合曲线
        prediction_value=sess.run(prediction,feed_dict={xs:x_data,ys:y_data})
        lines=ax.plot(x_data,prediction_value,'r-',lw=5)
        plt.pause(0.1)
```

有关张量、tf.reduce_sum的介绍，我是看这个链接的
作者：xingzai
链接：[https://www.jianshu.com/p/30b40b504bae](https://www.jianshu.com/p/30b40b504bae)

## 优化器
原始：
W=-learning_rate*dx
优化器：
STD:
传入样本数据的一部分

Momentum:
m=b1*m-learning_rate*dx
w+=m

AdaGrad:
v+=dx^2
W+=-learning_rate*dx/√v

RMSProp：
v=b1*v+(1-b1)*dx^2
W+=-learning_rate*dx/√v

Adam:
m=b1*m+(1-b1)*dx^2
v=b1*v+(1-b1)*dx^2
W+=-learning_rate*m/√v

## Tensorboard
Tensorboard将训练结果可视化，在网页中展示。
### 写入与读取

```python
writer=tf.compat.v1.summary.FileWriter("文件路径",sess.graph)
#将图表保存在路径
```
之后在cmd工具中:

```powershell
tensorboard --logdir=直接加文件路径
```

ps:
在安装时报错：
pip install package时Fatal error in launcher: Unable to create process using '"F:\python\python36\python.exe"
原因：我之前将将python.exe改为了python36.exe
解决方法：再改回来
### Graphs

在需要生成Graphs的变量前设置

```python
with tf.name_scope('变量名'):
	...
```
ps:
with ...可以嵌套使用，表示一个层下还有其他小的变量

### histogram&scalar
表示变量的分布区间和曲线

```python
tf.compat.v1.summary.histogram(图表中的名称,变量名)
tf.compat.v1.summary.scalar(曲线的名称,变量名)

merged=tf.compat.v1.summary.merge_all()
summary=sess.run(merged,feed_dict={xs:x_data,ys:y_data})
writer.add_summary(summary,global_step)#将histogram和scalar加入文件
```

## 分类问题
这也是莫烦老师的代码
链接：[https://www.bilibili.com/video/BV1Lx411j7ws?p=22](https://www.bilibili.com/video/BV1Lx411j7ws?p=22)
```python
import tensorflow as tf
from tensorflow.core.example.tutorials.mnist import input_data
from my_package.create_layer import add_layer

tf.compat.v1.disable_eager_execution

mnist=input_data.read_data_sets('MNIST_data',one_hot=True)

def compute_accuracy(v_xs,v_ys):
    """
    预测正确率
    :param v_xs:输入值 
    :param v_ys: 预测值
    :return: 正确率
    """
    global prediction
    y_pre=sess.run(prediction,feed_dict={xs:v_xs})
    correct_prediction=tf.equal(tf.argmax(y_pre,1),tf.argmax(v_ys,1))
    accuracy=tf.reduce_mean(tf.cast(correct_prediction,tf.float32))
    result=sess.run(accuracy,feed_dict={xs:v_xs,ys:v_ys})
    return result

#预存变量
xs=tf.compat.v1.placeholder(tf.float32,[None,784])
ys=tf.compat.v1.placeholder(tf.float32,[None,10])

#预测层添加
prediction=add_layer(xs,784,10,'layer1',activation_function=tf.nn.softmax)

#代价函数
cross_entropy=tf.reduce_mean(-tf.reduce_sum(ys*tf.log(prediction)))
#训练步骤
train_step=tf.compat.v1.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)

sess=tf.compat.v1.Session()
#变量初始化
sess.run(tf.compat.v1.global_variables_initializer)
for i in range(1000):
    batch_xs,batch_ys=mnist.train.next_batch(100)
    sess.run(train_step,feed_dict={xs:batch_xs,ys:batch_ys})
    if i%50==0:
        print(compute_accuracy(mnist.test.images,mnist.test.labels))
```








