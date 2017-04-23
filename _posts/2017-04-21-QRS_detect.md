---
title: 心率检测算法之P-T算法与So&Chan算法
tags: [算法]
---
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [1. Pan & Tompkin 算法(P-T 算法)](#1-pan-tompkin-算法p-t-算法)
	- [1.1 算法](#11-算法)
	- [1.2 优点](#12-优点)
	- [1.3 缺点](#13-缺点)
- [2. So & Chan 算法](#2-so-chan-算法)
	- [2.1 算法](#21-算法)
	- [2.2 优点](#22-优点)
	- [2.3 缺点](#23-缺点)

<!-- /TOC -->
近期由于项目原因重拾心电，补了一下拖了半年的P-T算法的知识，做了两种比较经典的基于时域的心电波形检测的算法的笔记。

# 1. Pan & Tompkin 算法(P-T 算法)

## 1.1 算法

*P-T 算法* 是一种双阈值检测QRS波群的实时算法。

首先采用带通滤波（通常为 5- 15 Hz）对心电信号进行滤波，微分（一阶差分）后再平方，平方过程可得出微分后的频响曲线斜率，并有助于区分由于一些高频分量引起的假阳性，再做移动窗口平均（150ms）积分，如果微分信号和积分信号分别同时达到阈值条件，就认为检测到一个QRS波，并且自动更新阈值。

![P-T算法流程图](http://ogw6sutvr.bkt.clouddn.com/P-T.jpg)

## 1.2 优点

1. Pan & Tompkins 算法能根据之前已检测到的QRS波信息自动更新阈值。

2. 根据心电图产生的原理，在检测到QRS波后的200ms内不做检测，以减少高T波引起的误判。

3. 采用回溯技术，若当前检测点与前一个QRS波的间期超过平均RR间期的1.66倍，就认为出现了漏检，需降低阈值从前一个QRS波处重新检测一次，以减少由于低幅QRS波造成的漏检。

## 1.3 缺点

由于其阈值初的设置技巧性很强，需要根据不同的信号手动调整，该算法的准确性还需进一步提升。

# 2. So & Chan 算法

## 2.1 算法

*So & Chan 算法* 是一种最大斜率与自适应阈值相结合的QRS波检测算法。

设原始心电信号为 X<sub>(n)</sub>, n 为采样点当前的位置。首先计算信号 X<sub>0</sub> 的斜率，公式如下：


<center>slope( n ) = − 2 X ( n − 2) − X ( n − 1) + X ( n + 1) + 2 X ( N + 2)</center>

接着确定斜率阈值:

<center>slope_thr = ( thr_param / 16 ) * max<sub>i</sub></center>


其中,参数 thr_param 一般为 2 , 4 , 8 或 16 。

如果连续两个点的斜率值均大于此阈值 slope_thr , 则以其中一点为 QRS 波的起始点 QRS_start 。 QRS_start 点一旦确定后,便开始向后检测原始心电信号 x<sub>0</sub> 中最大幅值点(即 R 波波峰),将该幅值设为 max<sub>i</sub> ,接着调整 max<sub>i</sub> 的值:

* 另一种说法是 当slope(n) > slope_thr 或者
slope<sup>2</sup>(n) > slope_thr<sup>2</sup> 时开始检测。



<center> max<sub>i</sub> = ( first_max<sub>i</sub> )/ filter_param + max<sub>i</sub> </center>


其中, first_max<sub>i</sub> 为 R 波振幅, max<sub>i</sub> 的初始值为原始心电信号 X <sub>0</sub> 中开始一段信号
(前 250 点)中斜率最大点的幅值。


## 2.2 优点

So & Chan 算法比较灵活,实现简单,处理速度较快,比较适合动态心电信号的处理。

## 2.3 缺点

当噪声大的时候对该算法影响较大,误检比较多。

> *参考文献* ：  
[1]  陈兵兵. 心电信号实时检测算法与应用研究[D]. 武汉:华中科技大学, 2009. 23-24   
[2] Ren-GUEY, Lee, I-CHOU, CHIEN-CHIH, LAI, MAING-HSIU, LIU, MING-JANG, CHIU-. A NOVEL QRS DETECTION ALGORITHM APPLIED TO THE ANALYSIS FOR HEART RATE VARIABILITY OF PATIENTS WITH SLEEP APNEA[J]. Institute of Computer and Communication Engineerin, 2005, 17(5): 45-46   
[3] CHUANG-CHIENCHIU, TONG-HONGLIN, ANDBEN-YILIAU. USING CORRELATION COEFFICIENT IN ECG WAVEFORM FOR ARRHYTHMIA DETECTION[J]. BIOMEDICAL ENGINEERINGAPPLICATIONS, BASIS & COMMUNICATIONS, 2005, 17(3): 38-39   
[4] So HH, Chan KL. Development of QRS detection method for real-time ambulatory
cardiac monitor. Engineering in Medicine and Biology Society, Proceedings of the
19 th Annual International Conference of the IEEE, 1997: 289-292.
