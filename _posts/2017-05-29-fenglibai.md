---
title: "风力摆实现笔记"
tags: [算法]
---

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [题目要求](#题目要求)
	- [任务](#任务)
	- [要求](#要求)
		- [基本要求](#基本要求)
		- [发挥部分](#发挥部分)
- [分析及方案](#分析及方案)
	- [分析](#分析)
	- [设计方案](#设计方案)
- [具体模式](#具体模式)
	- [题一](#题一)
	- [题二](#题二)
	- [题五](#题五)

<!-- /TOC -->
# 题目要求
## 任务
一长约 60cm~70cm 的细管上端用万向节
固定在支架上,下方悬挂一组(2~4 只)直流
风机,构成一风力摆,如图 1 所示。风力摆上
安装一向下的激光笔,静止时,激光笔的下端
距地面不超过 20cm。设计一测控系统,控制
驱动各风机使风力摆按照一定规律运动,激光
笔在地面画出要求的轨迹。

## 要求
### 基本要求
1.
从静止开始,15s 内控制风力摆做
类似自由摆运动,使激光笔稳定地
在地面画出一条长度不短于 50cm 的直线段,其线性度偏差不大于±
2.5cm,并且具有较好的重复性;

2. 从静止开始,15s 内完成幅度可控的摆动,画出长度在 30~60cm 间可设
置,长度偏差不大于±2.5cm 的直线段,并且具有较好的重复性;

3. 可设定摆动方向,风力摆从静止开始,15s 内按照设置的方向(角度)
摆动,画出不短于 20cm 的直线段;

4. 将风力摆拉起一定角度(30°~45°)放开,5s 内使风力摆制动达到静止状态。

### 发挥部分

1. 以风力摆静止时激光笔的光点为圆心,驱动风力摆用激光笔在地面画圆,30s 内需重复 3 次;圆半径可在 15~35cm 范围内设置,激光笔画出的轨迹应落在指定半径±2.5cm 的圆环内;

2. 在发挥部分(1)后继续作圆周运动,在距离风力摆 1~2m 距离内用一台 50~60W 台扇在水平方向吹向风力摆,台扇吹 5s 后停止,风力摆能够在 5s 内恢复发挥部分(1)规定的圆周运动,激光笔画出符合要求的轨迹;

3. 其他。

# 分析及方案

## 分析

自由摆运动实际上就是单摆运动，由单摆运动的周期可知：
*<center>T=2Pi sqrt(L/g)</center>*

我们用的细杆长度 *L* 为 70.5 cm, 激光笔下端距离地面的高度 *h* 为 19.5 cm, 则万向节距离地面的高度 *H* 为 90 cm。设重力加速度 *g=9.8 m/s<sup>2</sup>* , 则单摆周期：

*<center> T=2Pi sqrt(0.705/9.8) = 1.6852 s</center>*

频率 *f=1/T=1/1.6852=0.5934 Hz*。可知我们需要设计出一个带宽大于 0.5934Hz 的控制系统。角度采样率根据奈奎斯特采样定理，理论上选取 *fs>2f* 即可，但是题目中要求了系统最大调节时间，为了使得控制效果更好，需要取 *fs>10f* 甚至更高，在本次设计中采样率选取 200Sa/s，控制周期 T<sub>2</sub>=5ms。


## 设计方案

* 电机：820空心杯
* 传感器：MPU6050
* 数据传输：串口

# 具体模式

## 题一

![](http://ogw6sutvr.bkt.clouddn.com/%E9%A3%8E%E5%8A%9B%E6%91%86.png)

其中 *A* 为角度，*H* 为万向节到地面的高度，*L* 为细杆长度，*R* 为激光笔在地面的投射点到万向节的水平距离。
如图可知，只需使 *R>=25 cm*。

已知 *H* 和 *R* 根据三角公式可算出 *A*。 *A=arctan(R/H)*。

核心公式为：

*<center>Y=Asin(wt)</center>*


参考代码如下：

```c
const float priod = 1685.2;  //单摆周期(毫秒)
	static uint32_t MoveTimeCnt = 0;
	float set_y = 0.0;
	float A = 0.0;
	float Normalization = 0.0;
	float Omega = 0.0;

	MoveTimeCnt += 5;							 //每5ms运算1次
	Normalization = (float)MoveTimeCnt / priod;	 //对单摆周期归一化
	Omega = 2.0*3.14159*Normalization;			 //对2π进行归一化处理
	A = atan((R/88.0f))*57.2958f;				 //根据摆幅求出角度A,88为摆杆距离地面长度cm
	set_y = A*sin(Omega);                        //计算出当前摆角 	

	PID_M1_SetPoint(0);			//X方向PID定位目标值0
	PID_M1_SetKp(60);
	PID_M1_SetKi(0.79);	 
	PID_M1_SetKd(800);

	PID_M2_SetPoint(set_y);		//Y方向PID跟踪目标值sin
	PID_M2_SetKp(60);    
	PID_M2_SetKi(0.79);		
	PID_M2_SetKd(800); 	 

	M1.PWM = PID_M1_PosLocCalc(M1.CurPos);	//Pitch
	M2.PWM = PID_M2_PosLocCalc(M2.CurPos); //Roll

	if(M1.PWM > POWER_MAX)  M1.PWM =  POWER_MAX;
	if(M1.PWM < -POWER_MAX) M1.PWM = -POWER_MAX;

	if(M2.PWM > POWER_MAX)  M2.PWM = POWER_MAX;
	if(M2.PWM < -POWER_MAX) M2.PWM = -POWER_MAX;		

	MotorMove(M1.PWM,M2.PWM);
```



*MoveTimeCnt* 表示时间，因为采样频率为200Hz，每5ms进入一次中断，所以每次进入中断时	*MoveTimeCnt+=5* , 由 *w=2Pi/T* ,可知 *wt=2×3.14159/T×t*, *t* 为时间积分，即 *MoveTimeCnt* 。

其中，*57.2985=180/3.14159*,弧度转角度。

*omega* 即 *wt* ，由此可算得当前的摆动角度。调节PID参数，可以使其摆动到要求的角度。

* 这里具体怎么调PID还是不太清楚。

## 题二

![](http://ogw6sutvr.bkt.clouddn.com/%E9%A3%8E%E5%8A%9B%E6%91%862.png)

与题一相似，都只需要一个方向的电机。


> 李萨如(Lissajous)曲线（又称利萨茹图形、李萨如图形或鲍迪奇(Bowditch)曲线）是两个沿着互相垂直方向的正弦振动的合成的轨迹。       
它可由以下参数方程定义：
**<center>x=Asin(theta)</center>**
**<center>y=Bsin(n×theta+fi)</center>**   
其中，n>=1,fi<Pi/2。            
n称为曲线的参数，是两个正弦振动的频率比。  
若A=B，n=1时，则曲线是椭圆。
fi=0，fi=2Pi 或 fi=Pi 时，曲线为线段。   
![](http://ogw6sutvr.bkt.clouddn.com/lisaru.png)

根据 *李萨如图* 原理，只要控制A和B的比例，即可控制摆动的角度。


参考代码如下:

```c
MoveTimeCnt += 5;							 //每5ms运算1次
	Normalization = (float)MoveTimeCnt / priod;	 //对单摆周期归一化             *****使物理系统数值的绝对值变成某种相对值关系***         线性函数转换如下：y=(x-MinValue)/(MaxValue-MinValue)
	Omega = 2.0*3.14159*Normalization;			 //对2π进行归一化处理，把归一化频率转换为角频率
	A = atan((R/88.0f))*57.2958f;//根据摆幅求出角度A,88为摆杆离地高度                   						
	Ax = A*cos(angle*0.017453);	 //计算出X方向摆幅分量0.017453为弧度转换          2*pi/360
	Ay = A*sin(angle*0.017453);	 //计算出Y方向摆幅分量
	set_x = Ax*sin(Omega); 		 //计算出X方向当前摆角
	set_y = Ay*sin(Omega+Phase[pOffset]); //计算出Y方向当前摆角

```

其中，*angle* 即为需调节的角度。   
0.017453=Pi/180 ，角度转弧度。


## 题五

同样由李萨如图可知，当 *A=B*， *fi* 为 *Pi/2* 或 *3Pi/2* 时，可以画圆。其中，*Pi/2* 时为顺时针，*3Pi/2* 为逆时针。

参考代码如下：

```c
MoveTimeCnt += 5;							 //每5ms运算1次
	Normalization = (float)MoveTimeCnt / priod;	 //对单摆周期归一化
	Omega = 2.0*3.14159*Normalization;			 //对2π进行归一化处理				
	A = atan((R/88.0f))*57.2958f;    //根据半径求出对应的振幅A          57.2958f=360/(2*pi)     弧度转角度    A为杆子倾角

	if(RoundDir == 0)       	  
		phase = 3.141592/2.0;		 //逆时针旋转相位差90°
	else if(RoundDir == 1)  
		phase = (3.0*3.141592)/2.0;	 //顺时针旋转相位差270°

	set_x = A*sin(Omega);			 //计算出X方向当前摆角
	set_y = A*sin(Omega+phase); 	 //计算出Y方向当前摆角

```

* 此处代码都是用调节PID的方式控制位置直接进行画圆，老师提供的方案是先让风力摆摆出一定的初始距离，再进行画圆。

> 参考资料：[风力摆控制系统赛题分析](http://bbs.eeworld.com.cn/thread-476344-1-1.html)  
