---
title: "STM32F4 使用micropython"
tags: [单片机]
---

# MicroPython
MicroPython脱胎于Python，基于ANSI C（C语言标准），然后在语法上又遵循了Python的规范，主要是为了能在嵌入式硬件上（这里特指微控制器级别）更易于的实现对底层的操作。

电设国赛的时候接触到openmv，是使用micropython直接操纵stm32f7单片机。基于python解释性语言的特性，可以实时硬件调试。今天看到STM32F4也可以通过烧固件使用micropython。方法如下。

# Linux下固件烧录

下载 [micropython](https://github.com/micropython/micropython)。

```
cd micropython/stmhal/boards
git clone https://github.com/mcauser/BLACK_F407ZE.git
```


* 拔下STM32F4的USB，将BOOT0接到3.3v 。即进入DFU(Device Firmware Upgrade, 相当于USB的bootloader)模式。
* 插上USB

```
cd micropython/stmhal
make BOARD=BLACK_F407ZE
sudo make BOARD=BLACK_F407ZE deploy
```


第一个make创建了烧写固件需要的dfu文件和hex文件。

第二个make将dfu文件烧进单片机。(也可以用hex，二选一)

这里BLACK_F407ZE是创建了一个以此为名的文件夹，这里是指我用的板的名字。

* 拔掉USB，将BOOT0接GND
* 插上USB

```
screen /dev/ttyACM0
```

通过screen与单片机进行终端会话。这里ttyACM0是linux使用。可以通过查看端口号选择。

会话开始后就进入了一个python的REPL。同时弹出PYBFLASH的盘符。这是STM32F4的flash，这里看做了一个U盘，修改里面的main.py即可将py文件烧录进去，脱机运行。


> 参考资料 ：   
[MicroPython board definition for the Black STM32F407ZET6 board ](https://github.com/mcauser/BLACK_F407ZE)  
[在STM32F4 Disco上试用MicroPython](http://bbs.eeworld.com.cn/thread-480606-1-1.html)      
[micropython 官网](https://micropython.org/)    
[micropython documentation](http://docs.micropython.org/en/latest/pyboard/)
