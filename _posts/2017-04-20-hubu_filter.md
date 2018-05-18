---
title: 互补滤波概述
tags: [算法]
---

# 概述

互补滤波和卡尔曼滤波通常与MPU6050联系在一起。由于陀螺仪是积分计算角度，因此任意时刻的微小误差将会随着时间的推移在积分过程中被放大，导致陀螺仪数据产生漂移，因此长时间内陀螺仪的可信度较低。因此在实际应用中我们需要进行滤波。

但是卡尔曼滤波太复杂了，在下数学太差，没看懂。所以决定还是看看互补滤波吧。

互补滤波结合加速度计的准确性，和陀螺仪噪声小的特性，互取所长。

盗图一张：
![](http://img.blog.csdn.net/20160904133458193)

互补滤波器，就是上面积分器和低通滤波器的变种结合体。

简单地说，就是通过 *低通滤波器* 过滤掉短期波动，让长期变化得以保留。让信号一点一点起作用而不是一下子猛烈作用，即在时间范围上取平均。*积分器* 通过对该角度的积分得到系统运行以来的角度。而 *高通滤波器* 除去长期变化，保留短暂变化，加于陀螺仪，可以滤掉温漂；

# 公式

![](http://miaowlabs.com/img/wiki/complementary-filter-01.png)

其中，angle为当前角度，gyro为陀螺仪角速度，dt为计算周期。gyro*dt 得到计算周期时间段内通过的角度，通过对该角度的积分（不断累加），得到系统运行以来的角度。x_acc为当前加速度计换算后的角度值。对x_acc进行低通滤波，让加速度计的长期变化得以保留，angle能追踪加速度计的长期变化。

* 这里，0.98和0.02为自己取的系数，实际应用时需根据实际情况调参。

## 时间常数
这个时间常数确定了陀螺仪和加速度计的信任边界。信号时间周期低于dt时，陀螺仪占主导地位，加速度计的噪声除掉；信号时间周期大于dt时，加速度计的角度平均值就占据主导地位，有温漂的陀螺仪可以站一边去。


# 代码示例

{% highlight c %}

float Angle_gy;         //前一时刻的积分角度
float Angle_A,Angle_B;	//X.Y方向加速度暂存
float Angle_X,Angle_Y;	//最终倾角X.Y

/* 加速度计的返回值暂存变量 */
float JSDx = 0;		
float JSDy = 0;
float JSDz = 0;
/* 陀螺仪的返回值暂存变量 */
float TLYx = 0;
float TLYy = 0;
float TLYz = 0;


//互补滤波
//-------------------------------------------------------
//-------------------------------------------------------
float bias_cfx;
float angle_dotx; 		//外部需要引用的变量
const float dtx=0.0048;
//-------------------------------------------------------
void complement_filterX(float angle_m_cfx,float gyro_m_cfx)
{
	bias_cfx*=0.0001;			       //陀螺仪零飘低通滤波；500次均值；0.998
	bias_cfx+=gyro_m_cfx*0.009;		   //0.002
	angle_dotx=gyro_m_cfx-bias_cfx;		   
	Angle_X=angle_m_cfx*0.02 + (Angle_X+angle_dotx*dtx)*0.98;
	//加速度低通滤波；20次均值；按100次每秒计算，低通5Hz；0.90 0.05
}
//-------------------------------------------------------
//-------------------------------------------------------
float bias_cfy;
float angle_doty; 		//外部需要引用的变量
const float dty=0.0048;
//-------------------------------------------------------
void complement_filterY(float angle_m_cfy,float gyro_m_cfy)
{
	bias_cfy*=0.0001;			       //陀螺仪零飘低通滤波；500次均值；0.998
	bias_cfy+=gyro_m_cfy*0.009;		   //0.002
	angle_doty=gyro_m_cfy-bias_cfy;		   
	Angle_Y=angle_m_cfy*0.02 + (Angle_Y+angle_doty*dty)*0.98;
	//加速度低通滤波；20次均值；按100次每秒计算，低通5Hz；0.90 0.05
}

void Angle_Calcu(void)
{
	float temp1,temp2;
	/****************************Y方向**************************/
		//加速度(角度)			
	JSDx  = getAccX();	  //读取X轴加速度
	JSDy  = getAccY();	  //读取Y轴加速度
	JSDz  = getAccZ();	  //读取Z轴加速度
	temp1=sqrt((JSDx*JSDx+JSDz*JSDz))/JSDy;
	JSDy=atan(temp1)/3.1415926*180;
	if(JSDy>0) Angle_B=JSDy-89.9;
	if(JSDy<0) Angle_B=JSDy+90.1;
  //陀螺仪(角速度)
	TLYx = getGyroX();	      //静止时角速度Y轴输出为-30左右
	TLYx = (TLYx)/16.384;         //去除零点偏移，计算角速度值,负号为方向处理
////	Angle_gy = Angle_gy + TLYy*0.0056;  //角速度积分得到倾斜角度.

	/****************************X方向**************************/
		//加速度(角度)
	JSDx  = getAccX();	  //读取X轴加速度
	JSDy  = getAccY();	  //读取Y轴加速度
	JSDz  = getAccZ();	  //读取Z轴加速度
	temp2=sqrt((JSDy*JSDy+JSDz*JSDz))/JSDx;
	JSDx=atan(temp2)/3.1415926*180;
	if(JSDx>0) Angle_A=JSDx-87.2;
	if(JSDx<0) Angle_A=JSDx+92.8;
  //陀螺仪(角速度)
	TLYy = getGyroY();	      //静止时角速度Y轴输出为-30左右
	TLYy = -(TLYy)/16.384;         //去除零点偏移，计算角速度值,负号为方向处理
////	Angle_gy = Angle_gy + TLYy*0.0056;  //角速度积分得到倾斜角度.

		//-------互补滤波-----------------------

	//补偿原理是取当前倾角和加速度获得倾角差值进行放大，然后与
    //陀螺仪角速度叠加后再积分，从而使倾角最跟踪为加速度获得的角度
	//0.5为放大倍数，可调节补偿度；0.005为系统周期5ms

	Angle_X = Angle_X + (((Angle_A-Angle_X)*0.5 + TLYy)*0.0054);		//X方向
	Angle_Y =Angle_Y + (((Angle_B-Angle_Y)*0.5 + TLYx)*0.0054);	//Y方向

}


{% endhighlight %}
> 参考资料：  
[互补滤波器](http://miaowlabs.com/wiki/Mwbalanced/complementary-filter.html)  
[平衡车直立算法：互补平衡滤波](http://feichashao.com/balance_filter/)  
[互补滤波器——从RC电路到数字滤波器](http://blog.csdn.net/luoshi006/article/details/51459884#%E4%B8%80%E9%98%B6%E4%BD%8E%E9%80%9A%E6%BB%A4%E6%B3%A2%E5%99%A8)
