---
title: "工作环境配置与软件"
tags: [软件]
---

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [文本编辑器](#文本编辑器)
	- [Linux](#linux)
	- [Windows](#windows)
- [各语言编程](#各语言编程)
	- [C语言](#c语言)
		- [Linux](#linux)
		- [Windows](#windows)
		- [Android](#android)
	- [Java](#java)
		- [Linux](#linux)
	- [Python](#python)
		- [Linux](#linux)
	- [C sharp](#c-sharp)
		- [Linux](#linux)
		- [Windows](#windows)
- [单片机](#单片机)
	- [C51(STC)](#c51stc)
		- [Linux](#linux)
		- [Windows](#windows)
	- [STM32](#stm32)
		- [Linux](#linux)
		- [Windows](#windows)
	- [MSP430/MSP432](#msp430msp432)
	- [Arduino](#arduino)
- [上位机](#上位机)
	- [Android](#android)
	- [电脑端](#电脑端)
- [科学计算](#科学计算)
	- [Linux](#linux)
	- [Windows](#windows)
- [电路设计](#电路设计)
	- [Linux](#linux)
	- [Windows](#windows)
- [科学写作](#科学写作)
- [3D打印](#3d打印)
	- [Linux](#linux)
	- [Windows](#windows)
- [图片编辑](#图片编辑)
	- [Linux](#linux)
	- [Windows](#windows)

<!-- /TOC -->

自从有了apt-get这个神器，我装起软件来就像拿着张无限刷信用卡到了商场。导致很多时候过了一段时间就不知道以前装的这个软件是什么鬼，以及资源的严重浪费。为了以后工作环境的迁移和复制，决定做一个记录。

# 文本编辑器

## Linux

* Vim           
常用
* Atom           
我只是用来写MarkDown和html以及看代码
* Sublime3      
以前主要用来看代码，现在用Atom

## Windows
* Sublime3

# 各语言编程

## C语言

### Linux

* Vim + gcc/g++           

### Windows

* Dev C++      

### Android
* C4droid

## Java

### Linux
* Javac 命令行编译

## Python

### Linux

* python2.7  /Python3.5  命令行
* ipython

## C sharp   

### Linux   

* mono         

### Windows  

* Visual Studio (主要是写 ASP.NET )

# 单片机

## C51(STC)

### Linux  

* 编译： sdcc   
* 烧录： stcflash.py

### Windows

* keil

## STM32

### Linux

* SW4STM32(System Worbench For STM32)      
使用时注意要修改工程目录下的 YOUR_MCU.cfg ,将其最后一行

```
reset_config srst_only srst_nogate  
```

``` srst_nogate``` 去掉 ,否则调试失败。并且该软件只能调试无法烧录。

* STM32CubeMX: 用于生成hal库代码，支持生成SW4STM32工程文件。本来是Windows下的软件，但是在Linux下也可以用。
* ST-Link V2:用于调试

### Windows  

* MDK ：就是keil，和51的并存要改配置。现在的STM32的工程文件基本全是keil工程。

## MSP430/MSP432

* CCS V6 (多平台支持)
* msp430-gcc + mspdebug  :命令行，不如直接用IDE...

## Arduino

* Arduino IDE

# 上位机

## Android

* Android Studio (多平台)
* kivy  
python的一个UI框架，可以写跨平台app，试过，太麻烦。

## 电脑端   

* processing         
* matlab

# 科学计算           

## Linux

* ipython + 各种库
* matlab (太大了...)
* octave (matlab 的替代品，但是很多函数不能用，需要自己下载包，界面比较难看)
* R (几乎没怎么用过)          

## Windows    

* matlab

# 电路设计

## Linux  

* eagle (多平台): PCB设计。生成.brd 和 .sch 文件，网上有脚本可以转化为一般国内常用的 .pcbdoc  
* gerbv : 查看gerber文件      
* ngspice : 电路仿真，需要用命令行...  

## Windows  

* Protel (Altium Design): 国内最常用
* Design Spark  :生成 .pcb 文件

# 科学写作       
* Latex Studio  ：专业自动排版工具，有自己的语言，很麻烦
* MarkDown :随便找个编译器就行，在文本编辑器中加插件。
* org-mode : emacs 的插件 ，优点是目录树很清晰，可以导出成各种文件。缺点是功能太多，不如markdown简单方便。
* JabRef : 文献引用管理，私以为一般用不到，不如直接在线生成引用格式。

# 3D打印

## Linux

* blender :建模，功能太多太复杂

## Windows

* 123D :建模
* simply 3D :切片

# 图片编辑

## Linux

* Gimp :有点像PS，日常应用够了     

## Windows

* PS


> 以上，未完待续...
