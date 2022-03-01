---
title: tensorflw
author: not_you
date: 2021-12-18 00:00:00 +0800
categories: ["系统学习","机器学习"]
tags: [机器学习,深度学习]

---

系统学习一下tensorflw，做一个记录

### 线性回归

假设我们得到一个数据集，对应一个f(x)=ax+b的一次函数。对于这个问题，首先应当定义一个损失函数(loss function)或者叫代价函数（cost function）。一般一次函数使用均方差（f（x）-y）^2 。实现的模板如下

```python
x=data.x
y=data.y
model=tf.keras.Sequential() # 先定义一个空模型
model.add(tf.keras.layers.Dense(1,input_shape=(1,))) # 添加一个全连接层
model.compile(optimizer='adam',loss='mse') # 使用随机梯度，均方差
history = model.fit(x,y,epochs=5000) # 训练
```



### 多层感知器（神经网络）

线性回归可以理解为单个神经元，多个神经元放在同一层可以实现一个多分类器。但是单层的存在一个问题，即无法拟合异或运算

ps：西瓜书上10个神经元100个参数，是当成有向图来看的。即1-2的参数不等于2-1，因此有90个，再加上10个阈值

#### 激活函数

relu:屏蔽负值/sigmoid/tanh/leak relu：负值被缩小

一个通常的多层感知器的实现如下（以两层为例）

```python
model = tf.keras.Sequential([tf.keras.layers.Dense(10,input_shape =(3,).activation='relu'),tf.keras.layers.Dense(1)])# 第一层有40个参数，第二层有11个参数
model.compile(optimizer='adam',loss='mse') 
```



### 逻辑回归

 可以理解成一个二分类（比如是狗和不是狗）问题。 回答是或者否的问题。在这种条件下，均方差变得不合适，更适合使用交叉熵（在tf里为binary_crossentropy）。

 一个常见的实现如下：

```python
model=tf.keras.Sequential() # 先定义一个空模型 
model.add(tf.keras.layers.Dense(4,input_shape=(15,)，activation='relu')) # 添加一个全连接层,15是数据的输入变量,有64个参数
model.add(tf.keras.layers.Dense(4，activation='relu'))
model.add(tf.keras.layers.Dense(1，activation='sigmoid')) # 最后要映射到0和1
model.compile(optimizer='adam',loss='binary_crossentropy',metric=['acc']) # metric额外输出正确率 
```



### softmax分类

神经网络的原始输出并非概率值，实质上只是宿儒的数值做了复杂的加权与非线性处理。可以利用softmax层将其变为概率分布。多分类问题在tf中，损失使用categorical_crossentropy和apsrse_categorical_crossentropy，前者对应onehot编码

 以下是使用tf直接可以访问的数据集Fashion Mnist实现的例子

```python
(train_image,train_label), (test_image,test_label) = tf.keras.datasets.fashion_mnist.load_data # 加载数据
train_image = train_image/255 
test_image = test_image/255 # 相当于是归一化，因为图片里点的值都是0-255，而神经网络输入值最好在0-1之间
model=tf.keras.Sequential() 
model.add(tf.keras.layers.Flatten(input_shape=(28,28))# 将二维的图展为一个向量，因为这儿实际上是BP网络，所以需要展平
model.add(tf.keras.layers.Dense128，activation='relu'))
model.add(tf.keras.layers.Dense128，activation='softmax')
model.compile(optimizer='adam',loss='apsrse_categorical_crossentropy',metric=['acc'])

```

### 常用函数

```python
xxx = tf.keras.utils.to_categorical(xxx1) # tf内置的onehot编码

model.summary() # 查看模型的信息

model.evaluate() # 评估数据

np.argmax() # 获取一系列数里的最大值




```



