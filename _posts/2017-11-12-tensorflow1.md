---
title: "TensorFlow Note 1 "
tags: [算法]
---


# TensorFlow 概述

TensorFlow™ 是一个采用数据流图（data flow graphs），用于数值计算的开源软件库。节点（Nodes）在图中表示数学操作，图中的线（edges）则表示在节点间相互联系的多维数据数组，即张量（tensor）。

![](http://ogw6sutvr.bkt.clouddn.com/tensors_flowing.gif)

## TensorFlow 机制

### 1. Build graph using TensorFlow operations

### 2. feed data and run graph (operation)

* sess.run (op)
* sess.run (op, feed_dict={x: x_data})

其中，sess是一个Session。

### 3. update variables in the graph (and return values)


# 代码示例

## placeholder
placeholder 相当于一个占位符，使用方法如下：


```python
import tensorflow as tf
a=tf.placeholder(tf.float32)
b=tf.placeholder(tf.float32)
adder_node=a+b

sess=tf.Session()           # 建立对话
print(sess.run(adder_node,feed_dict={a:3,b:4}))    #第一步和第二步合在了一起
print(sess.run(adder_node,feed_dict={a:[1,3],b:[2,4]}))
```

    7.0
    [ 3.  7.]


与传统写法对比：


```python
# step 1
node1=tf.constant(3.0,tf.float32)
node2=tf.constant(4.0)
node3=tf.add(node1,node2)

#step 2
sess=tf.Session()
print("sess.run(node3):",sess.run(node3))

```

    sess.run(node3): 7.0


## Linear Regression

* Hypothesis
H(x)=Wx+b

* Cost function
cost=1/m*sum(H(x)-y)^2

用于评估哪种预测更好，其中H（x）为估计值，y为实际值。

最终要求cost的最小值，以求最好的线性回归。


### TensorFlow 中的写法如下

* y_model=tf.mul(X,w)
* cost=tf.square(Y-y_nodel)


```python
import tensorflow as tf
W = tf.Variable(tf.random_normal([1]), name='weight')
b = tf.Variable(tf.random_normal([1]), name='bias')
X = tf.placeholder(tf.float32, shape=[None])
Y = tf.placeholder(tf.float32, shape=[None])

# Our hypothesis XW+b
hypothesis = X * W + b
# cost/loss function
cost = tf.reduce_mean(tf.square(hypothesis - Y))
# Minimize
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)
train = optimizer.minimize(cost)

# Launch the graph in a session.
sess = tf.Session()
# Initializes global variables in the graph.
sess.run(tf.global_variables_initializer())

# Fit the line with new training data
for step in range(2001):
   cost_val, W_val, b_val, _ = sess.run([cost, W, b, train],
       feed_dict={X: [1, 2, 3, 4, 5],
                  Y: [2.1, 3.1, 4.1, 5.1, 6.1]})
   if step % 20 == 0:
       print(step, cost_val, W_val, b_val)

```

    0 99.1278 [-0.61782473] [-1.31166363]
    20 0.534242 [ 1.46245003] [-0.60686624]
    40 0.464802 [ 1.4410814] [-0.49261224]
    60 0.405915 [ 1.4122349] [-0.38829964]
    80 0.354489 [ 1.38523757] [-0.29082966]
    100 0.309578 [ 1.36000788] [-0.19974318]
    120 0.270357 [ 1.33643079] [-0.11462195]
    140 0.236104 [ 1.31439769] [-0.03507536]
    160 0.206192 [ 1.29380751] [ 0.03926166]
    180 0.180069 [ 1.2745657] [ 0.10873029]
    200 0.157255 [ 1.25658417] [ 0.17364934]
    220 0.137332 [ 1.23978031] [ 0.23431681]
    240 0.119933 [ 1.22407687] [ 0.29101115]
    260 0.104739 [ 1.20940197] [ 0.34399253]
    280 0.091469 [ 1.19568789] [ 0.39350402]
    300 0.0798806 [ 1.1828723] [ 0.43977299]
    320 0.0697603 [ 1.17089581] [ 0.48301181]
    340 0.0609222 [ 1.15970373] [ 0.52341884]
    360 0.0532038 [ 1.14924455] [ 0.56117952]
    380 0.0464633 [ 1.13947046] [ 0.59646738]
    400 0.0405767 [ 1.1303364] [ 0.62944406]
    420 0.035436 [ 1.12180054] [ 0.66026103]
    440 0.0309465 [ 1.11382389] [ 0.68905991]
    460 0.0270258 [ 1.10636938] [ 0.7159726]
    480 0.0236019 [ 1.09940314] [ 0.7411229]
    500 0.0206116 [ 1.09289312] [ 0.7646262]
    520 0.0180003 [ 1.0868094] [ 0.78659016]
    540 0.0157198 [ 1.08112419] [ 0.80711567]
    560 0.0137282 [ 1.07581139] [ 0.82629687]
    580 0.011989 [ 1.07084632] [ 0.84422183]
    600 0.01047 [ 1.06620657] [ 0.86097306]
    620 0.00914355 [ 1.06187057] [ 0.87662709]
    640 0.00798514 [ 1.05781865] [ 0.89125615]
    660 0.00697348 [ 1.05403209] [ 0.90492696]
    680 0.00608998 [ 1.05049336] [ 0.91770238]
    700 0.00531842 [ 1.04718661] [ 0.92964125]
    720 0.00464461 [ 1.04409635] [ 0.94079822]
    740 0.00405618 [ 1.04120827] [ 0.95122439]
    760 0.00354228 [ 1.03850961] [ 0.96096784]
    780 0.00309352 [ 1.03598762] [ 0.97007328]
    800 0.00270158 [ 1.03363073] [ 0.97858232]
    820 0.00235931 [ 1.03142822] [ 0.98653412]
    840 0.0020604 [ 1.02936995] [ 0.99396503]
    860 0.00179936 [ 1.02744651] [ 1.00090945]
    880 0.0015714 [ 1.02564895] [ 1.00739884]
    900 0.00137233 [ 1.02396929] [ 1.01346326]
    920 0.00119845 [ 1.02239943] [ 1.01913083]
    940 0.00104661 [ 1.02093244] [ 1.02442706]
    960 0.000914018 [ 1.01956153] [ 1.02937627]
    980 0.000798216 [ 1.01828051] [ 1.03400147]
    1000 0.000697096 [ 1.01708329] [ 1.03832364]
    1020 0.000608777 [ 1.01596463] [ 1.04236293]
    1040 0.000531647 [ 1.01491892] [ 1.04613769]
    1060 0.000464294 [ 1.01394188] [ 1.04966521]
    1080 0.000405469 [ 1.01302886] [ 1.05296171]
    1100 0.000354093 [ 1.01217556] [ 1.05604231]
    1120 0.000309238 [ 1.01137829] [ 1.05892098]
    1140 0.00027006 [ 1.01063299] [ 1.06161118]
    1160 0.000235848 [ 1.00993657] [ 1.06412518]
    1180 0.000205965 [ 1.00928581] [ 1.0664748]
    1200 0.00017987 [ 1.00867772] [ 1.06867063]
    1220 0.000157078 [ 1.00810933] [ 1.0707227]
    1240 0.000137177 [ 1.00757813] [ 1.07264018]
    1260 0.000119794 [ 1.00708187] [ 1.07443213]
    1280 0.000104616 [ 1.00661814] [ 1.07610667]
    1300 9.13632e-05 [ 1.00618458] [ 1.07767153]
    1320 7.97874e-05 [ 1.00577962] [ 1.07913387]
    1340 6.96787e-05 [ 1.00540102] [ 1.08050048]
    1360 6.08515e-05 [ 1.00504732] [ 1.08177745]
    1380 5.31403e-05 [ 1.00471675] [ 1.08297098]
    1400 4.64077e-05 [ 1.00440788] [ 1.08408618]
    1420 4.0529e-05 [ 1.00411916] [ 1.08512831]
    1440 3.53946e-05 [ 1.00384939] [ 1.08610225]
    1460 3.09101e-05 [ 1.00359738] [ 1.08701253]
    1480 2.69937e-05 [ 1.0033617] [ 1.08786309]
    1500 2.35728e-05 [ 1.00314152] [ 1.08865798]
    1520 2.05876e-05 [ 1.00293577] [ 1.08940089]
    1540 1.7977e-05 [ 1.00274336] [ 1.09009564]
    1560 1.56996e-05 [ 1.00256371] [ 1.09074414]
    1580 1.37107e-05 [ 1.00239587] [ 1.09135032]
    1600 1.19733e-05 [ 1.00223887] [ 1.0919168]
    1620 1.0457e-05 [ 1.00209224] [ 1.09244609]
    1640 9.13137e-06 [ 1.00195527] [ 1.09294093]
    1660 7.9744e-06 [ 1.00182724] [ 1.09340322]
    1680 6.96451e-06 [ 1.00170755] [ 1.09383512]
    1700 6.08205e-06 [ 1.00159574] [ 1.09423888]
    1720 5.31175e-06 [ 1.00149131] [ 1.09461606]
    1740 4.63792e-06 [ 1.00139356] [ 1.09496891]
    1760 4.0508e-06 [ 1.00130236] [ 1.09529841]
    1780 3.53763e-06 [ 1.00121701] [ 1.09560621]
    1800 3.08968e-06 [ 1.00113738] [ 1.09589386]
    1820 2.6985e-06 [ 1.00106299] [ 1.09616268]
    1840 2.35666e-06 [ 1.00099337] [ 1.09641385]
    1860 2.0578e-06 [ 1.00092828] [ 1.09664869]
    1880 1.79772e-06 [ 1.00086749] [ 1.09686804]
    1900 1.56959e-06 [ 1.00081074] [ 1.0970732]
    1920 1.37101e-06 [ 1.00075758] [ 1.09726477]
    1940 1.19746e-06 [ 1.0007081] [ 1.09744382]
    1960 1.04557e-06 [ 1.00066173] [ 1.09761107]
    1980 9.13208e-07 [ 1.00061834] [ 1.09776759]
    2000 7.97599e-07 [ 1.00057793] [ 1.09791374]


## Logistic Regression

![](http://ogw6sutvr.bkt.clouddn.com/logistic%20regression.png)


```python
x_data = [[1, 2], [2, 3], [3, 1], [4, 3], [5, 3], [6, 2]]
y_data = [[0], [0], [0], [1], [1], [1]]
# placeholders for a tensor that will be always fed.
X = tf.placeholder(tf.float32, shape=[None, 2])
Y = tf.placeholder(tf.float32, shape=[None, 1])

W = tf.Variable(tf.random_normal([2, 1]), name='weight')
b = tf.Variable(tf.random_normal([1]), name='bias')
# Hypothesis using sigmoid: tf.div(1., 1. + tf.exp(tf.matmul(X, W)))
hypothesis = tf.sigmoid(tf.matmul(X, W) + b)
# cost/loss function
cost = -tf.reduce_mean(Y * tf.log(hypothesis) + (1 - Y) * tf.log(1 - hypothesis))
train = tf.train.GradientDescentOptimizer(learning_rate=0.01).minimize(cost)

# Accuracy computation
# True if hypothesis>0.5 else False
predicted = tf.cast(hypothesis > 0.5, dtype=tf.float32)
accuracy = tf.reduce_mean(tf.cast(tf.equal(predicted, Y), dtype=tf.float32))

# Launch graph
with tf.Session() as sess:
   # Initialize TensorFlow variables
   sess.run(tf.global_variables_initializer())

   for step in range(10001):
       cost_val, _ = sess.run([cost, train], feed_dict={X: x_data, Y: y_data})
       if step % 200 == 0:
           print(step, cost_val)

   # Accuracy report
   h, c, a = sess.run([hypothesis, predicted, accuracy],
                      feed_dict={X: x_data, Y: y_data})
   print("\nHypothesis: ", h, "\nCorrect (Y): ", c, "\nAccuracy: ", a)

```

    0 0.950394
    200 0.691923
    400 0.598262
    600 0.549608
    800 0.518154
    1000 0.494249
    1200 0.474163
    1400 0.456294
    1600 0.439896
    1800 0.424593
    2000 0.410184
    2200 0.396553
    2400 0.383624
    2600 0.371345
    2800 0.359675
    3000 0.348578
    3200 0.338023
    3400 0.32798
    3600 0.318423
    3800 0.309324
    4000 0.300659
    4200 0.292404
    4400 0.284535
    4600 0.277031
    4800 0.269872
    5000 0.263038
    5200 0.25651
    5400 0.250272
    5600 0.244306
    5800 0.238598
    6000 0.233133
    6200 0.227898
    6400 0.22288
    6600 0.218066
    6800 0.213446
    7000 0.20901
    7200 0.204747
    7400 0.200648
    7600 0.196705
    7800 0.192909
    8000 0.189253
    8200 0.18573
    8400 0.182333
    8600 0.179056
    8800 0.175893
    9000 0.172838
    9200 0.169887
    9400 0.167033
    9600 0.164273
    9800 0.161603
    10000 0.159017

    Hypothesis:  [[ 0.03484504]
     [ 0.16406283]
     [ 0.32394117]
     [ 0.77279913]
     [ 0.93403745]
     [ 0.97832847]]
    Correct (Y):  [[ 0.]
     [ 0.]
     [ 0.]
     [ 1.]
     [ 1.]
     [ 1.]]
    Accuracy:  1.0


# Softmax Function

softmax用于多分类过程中，它将多个神经元的输出，映射到（0,1）区间内，可以看成概率来理解，从而来进行多分类。

![](http://ogw6sutvr.bkt.clouddn.com/softmax.png)


```python
x_data = [[1, 2, 1, 1], [2, 1, 3, 2], [3, 1, 3, 4], [4, 1, 5, 5], [1, 7, 5, 5],
                                                        [1, 2, 5, 6], [1, 6, 6, 6], [1, 7, 7, 7]]
y_data = [[0, 0, 1], [0, 0, 1], [0, 0, 1], [0, 1, 0], [0, 1, 0], [0, 1, 0], [1, 0, 0], [1, 0, 0]]

X = tf.placeholder("float", [None, 4])
Y = tf.placeholder("float", [None, 3])
nb_classes = 3

W = tf.Variable(tf.random_normal([4, nb_classes]), name='weight')
b = tf.Variable(tf.random_normal([nb_classes]), name='bias')

# tf.nn.softmax computes softmax activations
# softmax = exp(logits) / reduce_sum(exp(logits), dim)
hypothesis = tf.nn.softmax(tf.matmul(X, W) + b)

# Cross entropy cost/loss
cost = tf.reduce_mean(-tf.reduce_sum(Y * tf.log(hypothesis), axis=1))
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.1).minimize(cost)

# Launch graph
with tf.Session() as sess:
   sess.run(tf.global_variables_initializer())

   for step in range(2001):
       sess.run(optimizer, feed_dict={X: x_data, Y: y_data})
       if step % 200 == 0:
           print(step, sess.run(cost, feed_dict={X: x_data, Y: y_data}))

```

    0 3.32228
    200 0.582294
    400 0.485599
    600 0.410444
    800 0.341915
    1000 0.271736
    1200 0.224002
    1400 0.203896
    1600 0.186959
    1800 0.172513
    2000 0.160061
