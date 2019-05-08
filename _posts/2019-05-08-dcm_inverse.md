---
title: "DICOM图像反色问题"
tags: [BME]
---

# 反色问题

最近在做一些医学图像处理方面的工作，碰到过一个DICOM图像显示反色的问题。一般来说DICOM医学图像背景为暗部，而解剖部位为亮部，反色即其灰度倒转。很多DICOM浏览器会对这类图像进行一些处理，使其看上去正常，但是其灰度本质没有发生改变。

# 原因

这通常与DICOM文件的两个标签属性有关:

|Tag |Name | Value|
|---|--|---|
|(2050,0020)|Presentation LUT Shape | IDENTITY/INVERSE|
|(0028,0020)|Photometric Interpretation|MONOCHROME1/MONOCHROME2|

通常情况下，其值分别为 **IDENTITY** 和 **MONOCHROME1** 。而反色图像的值分别为 **INVERSE** 和 **MONOCHROME2**。

其中， **Photometric Interpretation** 属性告诉我们如何显示图像的Pixel Data，**MONOCHROME1** 和 **MONOCHROME2** 表示这是一个灰度图，在 **MONOCHROME2** 中，Pixel 值越大，图像就越亮，而 **MONOCHROME1** 相反。

# 解决方案

## 修改标签属性
首先，我们应该将 Photometric Interpretation 的值修改为 *MONOCHROME2*, 此时亮度反转，但是灰度可能仍然不正常，亮部过亮而无法看出某些信息。而修改 Presentation LUT Shape 属性不会有任何改变。

## 修改灰度值
另外一种方法是进行灰度转换操作，当检测到反色图像后，获取其Pixel Data 最大值和最小值，另图像中的Pixel值为：

<center>Pixel=最大值+最小值-当前值。</center>


此时灰度显示值为正常。

> 参考文献：
[为什么某些CR/DR图像打开后是反色的](https://blog.csdn.net/Sayesan/article/details/50680224)
[Photometric Interpretation and Presentation LUT Shape](https://groups.google.com/forum/#!topic/comp.protocols.dicom/Wzt_06RbF5c)
