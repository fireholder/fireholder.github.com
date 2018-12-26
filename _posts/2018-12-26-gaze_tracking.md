---
title: "基于CNN的视线跟踪系统"
tags: [算法]
---

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [视线跟踪技术概述](#视线跟踪技术概述)
	- [二维模型](#二维模型)
		- [特点](#特点)
		- [2-D 视线跟踪算法](#2-d-视线跟踪算法)
			- [瞳孔-角膜反射法](#瞳孔-角膜反射法)
			- [普金野像法](#普金野像法)
			- [虹膜-巩膜边缘法](#虹膜-巩膜边缘法)
			- [基于人工神经网络的方法](#基于人工神经网络的方法)
	- [三维模型](#三维模型)
		- [特点](#特点)
- [卷积神经网络概述](#卷积神经网络概述)
	- [CNN结构](#cnn结构)
		- [卷积层 （convolutional Layer）](#卷积层-convolutional-layer)
			- [卷积各层](#卷积各层)
		- [池化层（Pooling Layer）](#池化层pooling-layer)
		- [全连接层（Fully-Connected Layer）](#全连接层fully-connected-layer)
	- [训练过程](#训练过程)
- [视线跟踪方案](#视线跟踪方案)
	- [人脸识别模块](#人脸识别模块)
	- [CNN模块](#cnn模块)
		- [数据导入与变换](#数据导入与变换)
			- [JSON解析数据](#json解析数据)
			- [数据变换](#数据变换)
		- [CNN模型](#cnn模型)
		- [自动化调参](#自动化调参)
		- [运行结果](#运行结果)
- [参考资料](#参考资料)

<!-- /TOC -->

# 视线跟踪技术概述
## 二维模型
### 特点
1. 测量的是视线与屏幕的交点
2. 可采用单目相机、单光源
3. 平面特征提取速度快、计算量小

### 2-D 视线跟踪算法
#### 瞳孔-角膜反射法

首先通过红外光源照射人的眼睛产生暗瞳效应，并在眼睛角膜上产生亮斑，然后摄像机拍摄人眼图像并进行图像处理，提取出人眼瞳孔中心和亮斑两个中心，这两个中心点会形成一个二维向量，设这个向量为v，然后再通过视线映射函数，根据瞳孔-角膜光斑向量v计算出眼睛视线方向。

这种视线跟踪方法在头部保持不动的时候具有较高的精度，但是当头部运动时，之前通过校准程序求出的映射函数已经不再适用于此刻的头部位置，将会出现比较大的误差。

#### 普金野像法
普金野像是指当光线通过不同的眼球组织时，产生的不同的光源反射影像。

双普金野眼动仪就是通过这种方法来进行视线跟踪的测量仪器。但是价格非常昂贵。

#### 虹膜-巩膜边缘法
首先通过红外源产生不可见的红外光照射眼睛，然后在眼睛附近安置两只红外光敏晶体管，分别用来检测虹膜与巩膜边缘处的左右两侧反射的红外光。当眼睛向左右转动时，光敏晶体管所接收的红外光线就会发生变化，当眼睛向右转动时，虹膜向眼睛右边移动，因虹膜反射的红外光线要少于巩膜，所以右边的光敏晶体管所接收的红外线就会减少，而左边的光敏晶体管所接收的红外线就会增加。通过这个差分信号就可以无接触的检测出视线方向。

这种算法存在较大误差，在垂直方向上跟踪精度低，并且对使用者具有干扰性。

#### 基于人工神经网络的方法
这是本文采取的方法。这里我们将输入图像作为网络的特征向量，这种方法通常u不需要建立视线和目标平面的映射关系，并且头部可以在小范围内自然的运动。

## 三维模型
### 特点
1. 测量视线在空间中的方向
2. 需使用双目相机、双光源以构建眼球和角膜球面的空间位置
3. 立体特征提取速度慢，并且需要建立三维模型，计算量大

# 卷积神经网络概述

## CNN结构
<center>INPUT - CONV - RELU - POOL -FC </center>

### 卷积层 （convolutional Layer）
我们设计一个想要识别特定特征的滤波器，当它移动到该位置时，按照矩阵操作，将整个区域的图像像素与滤波器相乘，我们得到一个很大的值，而当它移动到其他区域时，我们得到一个相对很小的值。

在训练CNN的某个卷积层时，我们实际上是在训练一系列的滤波器。没一个滤波器的输出堆叠在一起，形成卷积图像纵深维度。对于一个输入为32×32×3（3为RGB通道数）的图像，假如我们在CNN的第一层定义训练12个滤波器，那这一层的输出就是32×32×12。

#### 卷积各层
- 第一个卷积层：检测低阶特征（如边、角、曲线等）
- 第二个卷积层：输入第一个卷积层的输出（滤波器激活图），检测低阶特征的组合等情况（半圆、四边形等）

随着卷积层的增加，对应滤波器检测的特征就更加复杂，最后一层滤波器按照训练CNN目的的不同，在检测到相应视线时激活。

### 池化层（Pooling Layer）
即取平均区域或最大。

有时图像太大，我们需要减少训练参数的数量，池化的唯一目的就是减少图像的空间大小。

### 全连接层（Fully-Connected Layer）
分类器，一个简单的BP网络。

## 训练过程

定义一个损失函数，假定L是这个损失函数的输出，我们的目的是让L的值反馈给整个卷积神经网络，以修改各个滤波器的权重，使得L最小。

# 视线跟踪方案

基于lookie-lookie，在浏览器中进行，通过网络摄像头进行人脸识别，通过键盘敲击采集人眼图像以及当前鼠标所在坐标。将人眼图像和注视点坐标作为输入数据和label保存为json文件，通过python读取json，并进行模型建立和训练，找出最优方案，将模型文件导入原JS中，即可进行预测。

## 人脸识别模块
选用 **clmtrakr** JS库

## CNN模块

在JS中选用 **tensorflow.js** 库，在python中使用keras库，以tensorflow作为引擎。

### 数据导入与变换
输入数据分为训练集和验证集，比例为4：1。

#### JSON解析数据

{% highlight python %}
with open('../数据/dataset.json',encoding='utf-8') as f:
    line = f.readline()
    dataset=json.loads(line)
    train_x=dataset['train']['x'][0]
    train_y=dataset['train']['y']
    train_n=dataset['train']['n']
    val_x=dataset['val']['x'][0]
    val_y=dataset['val']['y']
    val_n=dataset['val']['n']
    shape_train_x=dataset['train']['shapes']['x0']
    shape_val_x=dataset['val']['shapes']['x0']
    shape_train_y=dataset['train']['shapes']['y']
    shape_val_y=dataset['val']['shapes']['y']
    f.close()

{% endhighlight %}

#### 数据变换

将原本的list转换为array，并进行维度转换。
{% highlight python %}
train_x=np.array(train_x).reshape(np.array(shape_train_x))
train_y=np.array(train_y).reshape(np.array(shape_train_y))
{% endhighlight %}

### CNN模型

其中，卷积层激活函数为 `relu`，全连接层为 `tanh`。

{% highlight python %}
from keras.layers import Dense, Activation, Convolution2D, MaxPooling2D, Flatten,Dropout
from keras.models import Sequential

def create_model():
    model= Sequential()
    model.add(Convolution2D(
        #batch_input_shape=(64, 1, 28, 28),
        filters=20,
        kernel_size=5,
        strides=1,
        input_shape=[25,50,3],
    ))
    model.add(Activation('relu'))

    model.add(MaxPooling2D(
        pool_size=[2,2],
        strides=[2,2],
))

    model.add(Flatten())
    model.add(Dropout(0.2))

    # 2 output x,y
    model.add(Dense(units=2, activation='tanh'))

    model.compile(optimizer=keras.optimizers.Adam(0.0005),loss='mean_squared_error',metrics=['accuracy'])

    return model
{% endhighlight %}

### 自动化调参

* 使用 `Grid Search`

{% highlight python %}
from keras.wrappers.scikit_learn import KerasClassifier

model = KerasClassifier(build_fn=create_model)

from sklearn.model_selection import GridSearchCV

batch_size = [10, 20, 30, 40, 50, 60 ]
epochs = [32, 64, 96, 128,160, 192, 224, 256]
param_grid = dict(batch_size=batch_size, epochs=epochs)
grid = GridSearchCV(estimator=model, param_grid=param_grid,n_jobs=1)
grid_result = grid.fit(train_x, train_y)

print('Best: {} using {}'.format(grid_result.best_score_, grid_result.best_params_))
means = grid_result.cv_results_['mean_test_score']
stds = grid_result.cv_results_['std_test_score']
params = grid_result.cv_results_['params']

for mean, std, param in zip(means, stds, params):
    print("%f (%f) with: %r" % (mean, std, param))

{% endhighlight %}

针对170个数据量，一次训练40个，训练224次时效果最好。此时达到 loss：0.0038, acc：0.9412。

### 运行结果

```sh
Best: 0.8000000036814634 using {'batch_size': 40, 'epochs': 224}
0.770588 (0.126744) with: {'batch_size': 10, 'epochs': 32}
0.770588 (0.151178) with: {'batch_size': 10, 'epochs': 64}
0.794118 (0.108803) with: {'batch_size': 10, 'epochs': 96}
0.794118 (0.103169) with: {'batch_size': 10, 'epochs': 128}
0.800000 (0.121415) with: {'batch_size': 10, 'epochs': 160}
0.788235 (0.111438) with: {'batch_size': 10, 'epochs': 192}
0.788235 (0.137764) with: {'batch_size': 10, 'epochs': 224}
0.800000 (0.121415) with: {'batch_size': 10, 'epochs': 256}
0.800000 (0.110747) with: {'batch_size': 20, 'epochs': 32}
0.752941 (0.151083) with: {'batch_size': 20, 'epochs': 64}
0.770588 (0.151178) with: {'batch_size': 20, 'epochs': 96}
0.800000 (0.135033) with: {'batch_size': 20, 'epochs': 128}
0.770588 (0.151178) with: {'batch_size': 20, 'epochs': 160}
0.794118 (0.143144) with: {'batch_size': 20, 'epochs': 192}
0.770588 (0.111357) with: {'batch_size': 20, 'epochs': 224}
0.770588 (0.124374) with: {'batch_size': 20, 'epochs': 256}
0.747059 (0.070097) with: {'batch_size': 30, 'epochs': 32}
0.764706 (0.138537) with: {'batch_size': 30, 'epochs': 64}
0.776471 (0.143047) with: {'batch_size': 30, 'epochs': 96}
0.776471 (0.132563) with: {'batch_size': 30, 'epochs': 128}
0.770588 (0.140566) with: {'batch_size': 30, 'epochs': 160}
0.782353 (0.124596) with: {'batch_size': 30, 'epochs': 192}
0.758824 (0.127900) with: {'batch_size': 30, 'epochs': 224}
0.782353 (0.134934) with: {'batch_size': 30, 'epochs': 256}
0.764706 (0.114020) with: {'batch_size': 40, 'epochs': 32}
0.741176 (0.151128) with: {'batch_size': 40, 'epochs': 64}
0.764706 (0.134836) with: {'batch_size': 40, 'epochs': 96}
0.770588 (0.151178) with: {'batch_size': 40, 'epochs': 128}
0.758824 (0.142950) with: {'batch_size': 40, 'epochs': 160}
0.776471 (0.132563) with: {'batch_size': 40, 'epochs': 192}
0.800000 (0.121415) with: {'batch_size': 40, 'epochs': 224}
0.770588 (0.140566) with: {'batch_size': 40, 'epochs': 256}
0.652941 (0.100713) with: {'batch_size': 50, 'epochs': 32}
0.770588 (0.151178) with: {'batch_size': 50, 'epochs': 64}
0.770588 (0.124374) with: {'batch_size': 50, 'epochs': 96}
0.770588 (0.124374) with: {'batch_size': 50, 'epochs': 128}
0.770588 (0.151178) with: {'batch_size': 50, 'epochs': 160}
0.770588 (0.140566) with: {'batch_size': 50, 'epochs': 192}
0.782353 (0.145956) with: {'batch_size': 50, 'epochs': 224}
0.782353 (0.134934) with: {'batch_size': 50, 'epochs': 256}
0.647059 (0.122212) with: {'batch_size': 60, 'epochs': 32}
0.747059 (0.096635) with: {'batch_size': 60, 'epochs': 64}
0.752941 (0.144735) with: {'batch_size': 60, 'epochs': 96}
0.782353 (0.134934) with: {'batch_size': 60, 'epochs': 128}
0.776471 (0.132563) with: {'batch_size': 60, 'epochs': 160}
0.764706 (0.148601) with: {'batch_size': 60, 'epochs': 192}
0.782353 (0.115125) with: {'batch_size': 60, 'epochs': 224}
0.782353 (0.121321) with: {'batch_size': 60, 'epochs': 256}
```

# 参考资料
> [CNN](http://www.zhihu.com/question/39022858)
[cs321n CNN](http://cs231n.github.io/convolutional-networks/#overview)
[lookie-lookie](https://cpury.github.io/learning-where-you-are-looking-at/)
[tensorflow.js to keras](https://js.tensorflow.org/tutorials/tfjs-layers-for-keras-users.html)
[tensorflow.js core concepts](https://js.tensorflow.org/tutorials/core-concepts.html)
