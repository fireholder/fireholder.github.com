---
title: "步进电机学习笔记"
tags: [硬件]
---

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [步进电机的静态指标术语](#步进电机的静态指标术语)
- [分类](#分类)
	- [根据结构分类](#根据结构分类)
		- [反应式](#反应式)
		- [永磁式](#永磁式)
		- [混合式](#混合式)
	- [根据驱动方式分类](#根据驱动方式分类)
		- [单极性驱动](#单极性驱动)
		- [双极性驱动](#双极性驱动)
			- [细分驱动](#细分驱动)
- [工作原理](#工作原理)
	- [单相通电](#单相通电)
	- [双相通电](#双相通电)
	- [半步步进](#半步步进)
- [时序(两相四线步进电机)](#时序两相四线步进电机)
	- [8拍](#8拍)
	- [4拍](#4拍)

<!-- /TOC -->

步进电机是一种专门用于位置和速度精确控制的特种电机。步进电机的最大特点是其“数字性”,对于微
电脑发过来的每一个脉冲信号,步进电机在其驱动器的推动下运转一个固定角度(简称一步)。

# 步进电机的静态指标术语
* **相数**: 产生不同对极N、S磁场的激磁线圈对数。
* **拍数**：完成一个磁场周期性变化所需脉冲数，或指电机转过一个齿距角所需脉冲数。以四相电机为例，有四相四拍运行方式即 _AB-BC-CD-DA-AB_ ,四相八拍运行方式即 _A-AB-B-BC-C-CD-D-DA-A_ 。
* **步距角**: 对应一个脉冲信号。*theta=360/(转子齿数×运行拍数)* 。

* **力矩**:
力矩是摩擦力矩(Tf)和惯性力矩(Ti)之和。摩擦力矩(oz-in 或 g-cm)为要求移动一个载荷的力(单位为 oz 或 g)乘以用于驱动载荷的力臂(r)长度(单位为 in 或 cm)。*Tf=F×r* , 惯性力矩(Ti)是用于加速负载(单位为:g-cm2)而需要的力矩, *Ti=I(w/t)Pi×theta×K* 。其中，I为转动惯量，w为步进速率，theta为步进角度，K为常数93.73。


# 分类

## 根据结构分类

### 反应式

定子上有绕组、转子由软磁材料组成。结构简单、成本低、步距角小,可达 1.2°、但动态性能差、效率
低、发热大,可靠性难保证。

### 永磁式
永磁式步进电机的转子用永磁材料制成,转子的极数与定子的极数相同。其特点是动态性能好、输出力矩
大,但这种电机精度差,步矩角大(一般为 7.5°或 15°)。

### 混合式
混合式步进电机综合了反应式和永磁式的优点,其定子上有多相绕组、转子上采用永磁材料,转子和定子
上均有多个小齿以提高步矩精度。其特点是输出力矩大、动态性能好,步矩角小,但结构复杂、成本相对
较高。

* 最受欢迎的是 *两相混合式步进电机* 。

## 根据驱动方式分类

### 单极性驱动
单极性驱动分为整步驱动和半步驱动两大类。

在任意时刻,只给一个线圈通电,其他
三个线圈都没有被通电,单极性整步驱动比后面介绍的双极性整步驱动的转矩要小。

### 双极性驱动

双极性驱动分为整步驱动、半步驱动和细分驱动三大类。

此电路和直流电机双方向驱动电路类似,
采用 Q1~Q8 八个三极管组成两个 H 桥,分别驱动 AC、BD 两路线圈,电流方向由 H 桥控制。
。为了降低工作电流,线圈分别串接了 R11 和 R18。和单极性驱动电路相比,双
极性驱动电路更复杂,但转矩更大。

![](http://ogw6sutvr.bkt.clouddn.com/%E6%AD%A5%E8%BF%9B%E7%94%B5%E6%9C%BA%E5%8F%8C%E6%9E%81%E6%80%A7%E9%A9%B1%E5%8A%A8%E7%94%B5%E8%B7%AF.png)

#### 细分驱动

改变定子线圈的电流比例,让
转子在旋转过程中,可以停靠在一个整步中不同位置,
把一个整步分成多个小步来跑。

# 工作原理

以两相四线(双极性)1.8度步进电机举例：
![两相四线步进电机](http://ogw6sutvr.bkt.clouddn.com/%E4%B8%A4%E7%9B%B8%E5%9B%9B%E7%BA%BF%E6%AD%A5%E8%BF%9B%E7%94%B5%E6%9C%BA.png)

当其绕组的通电方向顺序按照 AC->BD->CA->DB 四个状态周而复始进行变化,每变化
一次,电机运转一步,即 1.8 度。

## 单相通电
一个两相电机的典型的步进顺序。在第 1 步中,两相定
子中的 A 相被通电,因异性相吸,其磁场将转子固定在图示位置。当 A 相关闭、B 相被通电时,转子顺时针旋转 90°。在第 3 步中,B 相关闭、A 相被通电,但极性与第 1 步相反,这促使转子再次旋转 90°。在第 4 步中,A 相关闭、B相通电,极性与第 2 步相反。重复该顺序促使转子按 90°的步距角顺时针旋转。

![](http://ogw6sutvr.bkt.clouddn.com/%E4%B8%A4%E7%9B%B8%E7%94%B5%E6%9C%BA%E5%8D%95%E7%9B%B8%E9%80%9A%E7%94%B5.png)

## 双相通电
更常用的步进方法是“双相通电”,即电机的两相一直通电。但是,一次只能转换一相的极性,两相步进时,转子与定子两相之间的轴线处对直。由于两相一直通电,本方法比“单相通电”步进多提供了 41.1%的力矩,但输入功率为 2 倍。


![](http://ogw6sutvr.bkt.clouddn.com/%E4%B8%A4%E7%9B%B8%E7%94%B5%E6%9C%BA%E5%8F%8C%E7%9B%B8%E9%80%9A%E7%94%B5.png)

## 半步步进
电机也可以转换相位之间插入一个关闭状态而走“半步”。这将步进电机的整个步距角一分为二。例如,
一个 90°的步进电机将每半步移动 45°。但是,与“两相通电”相比,半步进通常导致 15%-30%
的力矩损失(取决于步进速率)。在每交换半步的过程中,由于其中一个绕组没有通电,所以作用在转子
上的电磁力要小,造成了力矩的净损失。

# 时序(两相四线步进电机)

## 8拍

| |1   | 2|3|4|5|6|7|8|
| :-- | :- |
| A | 1|1|0|0|0|0|0|0|
|A-|0|0|0|1|1|1|0|0|
|B|0|1|1|1|0|0|0|0|
|B-|0|0|0|0|0|1|1|1|

## 4拍

|  | 1|2 |3 |4 |
| :--| :----- |
| A   | 1| 0 |0 |1|
|A- |0|1|1|0|
|B|1|1|0|0|
|B-|0|0|1|1|
