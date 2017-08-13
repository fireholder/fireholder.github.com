---
title: "2017全国大学生电子设计竞赛中的一些问题总结"
tags: [单片机]
---

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [1. STM32F1 ADC多通道DMA问题](#1-stm32f1-adc多通道dma问题)
	- [代码](#代码)
	- [关键步骤](#关键步骤)
- [中断优先级问题](#中断优先级问题)
- [PWM输出问题](#pwm输出问题)

<!-- /TOC -->

国赛gg,不过还是要总结一些遇到的问题。

# 1. STM32F1 ADC多通道DMA问题

STM32F103 有2个DMA，分别可以使用ADC1和ADC3这两个通道。ADC1对应通道1,而ADC2对应通道5。多通道DMA理论上不是什么问题，网上有很多例程。但是在实际运用的过程中，一开始在使用ADC1的多通道DMA时，只能发送第一个通道的数据。搜索多方资料，百思不得其解。配置是完全一样的，后来觉得可能是配置的顺序问题。而ADC3的DMA最后也没能用。最后可以用的代码如下：

## 代码

{% highlight c %}


void ADC_DMA_Config_x(void)
{
  GPIO_InitTypeDef GPIO_InitStructure;
 ADC_InitTypeDef ADC_InitStructure;

 RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE);

 GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0|GPIO_Pin_1 | GPIO_Pin_2 | GPIO_Pin_3;
 GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AIN;
 GPIO_Init(GPIOC, &GPIO_InitStructure);



 DMA_InitTypeDef DMA_InitStructure;

 RCC_AHBPeriphClockCmd(RCC_AHBPeriph_DMA1, ENABLE);
 DMA_DeInit(DMA1_Channel1);
 DMA_InitStructure.DMA_PeripheralBaseAddr = (uint32_t)&ADC1->DR;
 DMA_InitStructure.DMA_MemoryBaseAddr = (u32)&adc_x;  //DMA内存地址,即传送数据要存储的地址
 DMA_InitStructure.DMA_DIR = DMA_DIR_PeripheralSRC; //外设作为数据传输的来源
 DMA_InitStructure.DMA_BufferSize = 4;//指定DMA通道的DMA缓存的大小，即一次要传送几个数据
 DMA_InitStructure.DMA_PeripheralInc = DMA_PeripheralInc_Disable; //外设地址寄存器递增
 DMA_InitStructure.DMA_MemoryInc = DMA_MemoryInc_Enable; //内存地址寄存器递增
 DMA_InitStructure.DMA_PeripheralDataSize = DMA_PeripheralDataSize_HalfWord;//外设数据宽度16
 DMA_InitStructure.DMA_MemoryDataSize = DMA_MemoryDataSize_HalfWord;//存储数据宽度16
 DMA_InitStructure.DMA_Mode = DMA_Mode_Circular;//工作在循环缓存模式
 DMA_InitStructure.DMA_Priority = DMA_Priority_High; //DMA通道x拥有高优先级
 DMA_InitStructure.DMA_M2M = DMA_M2M_Disable; //DMA通道x没有设置为内存到内存传输
 DMA_Init(DMA1_Channel1, &DMA_InitStructure); //ADC1在DMA1通道1内
 DMA_Cmd(DMA1_Channel1,ENABLE);  //使能DMA1

 RCC_APB2PeriphClockCmd(RCC_APB2Periph_ADC1, ENABLE);
// ADC_DeInit(ADC1);
 ADC_InitStructure.ADC_Mode = ADC_Mode_Independent;//ADC独立工作模式
 ADC_InitStructure.ADC_ScanConvMode = ENABLE;//开启扫描模式
 ADC_InitStructure.ADC_ContinuousConvMode = ENABLE;//连续转换
 ADC_InitStructure.ADC_ExternalTrigConv = ADC_ExternalTrigConv_None;//??????
 ADC_InitStructure.ADC_DataAlign = ADC_DataAlign_Right;//ADC数据右对齐
 ADC_InitStructure.ADC_NbrOfChannel = 4;//ADC通道数
 ADC_Init(ADC1, &ADC_InitStructure);


 RCC_ADCCLKConfig(RCC_PCLK2_Div6); //12M  最大14M 设置ADC时钟（ADCCLK）
 //设置指定ADC的规则组通道，设置它们的转化顺序和采样时间，这里对不同的通道对应的引脚要查表
 ADC_RegularChannelConfig(ADC1, ADC_Channel_10, 1, ADC_SampleTime_239Cycles5);//SampleTime为采样周期
 ADC_RegularChannelConfig(ADC1, ADC_Channel_11, 2, ADC_SampleTime_239Cycles5);
 ADC_RegularChannelConfig(ADC1, ADC_Channel_12, 2, ADC_SampleTime_239Cycles5);
 ADC_RegularChannelConfig(ADC1, ADC_Channel_13, 4, ADC_SampleTime_239Cycles5);
// ADC_RegularChannelConfig(ADC1, ADC_Channel_16, 6, ADC_SampleTime_239Cycles5);

 ADC_DMACmd(ADC1, ENABLE);  //将ADC与DMA链接在一起

 ADC_Cmd(ADC1, ENABLE);
 ADC_ResetCalibration(ADC1);   //重置指定的ADC的校准寄存器
 while(ADC_GetResetCalibrationStatus(ADC1)); //获取ADC重置校准寄存器的状态
 ADC_StartCalibration(ADC1); //开始指定ADC的校准状态
 while(ADC_GetCalibrationStatus(ADC1));  //获取指定ADC的校准程序
 ADC_SoftwareStartConvCmd(ADC1, ENABLE);//使能或者失能指定的ADC的软件转换启动功能

}

{% endhighlight %}

## 关键步骤

* 开启扫描模式
* 开启连续传送模式
* 设置通道数
* 设置通道传送的顺序和周期(有的解释说周期太短传送有问题，这里239是最大周期)
* 外设地址寄存器递增

# 中断优先级问题

在使用openmv3摄像头串口传送小球定点坐标数据时，数据发生了严重的错乱和丢包。一开始以为是LCD屏的问题（去除LCD后确实好很多），可是理论上图像显示对串口的数据传输不会有那么大的影响。后来觉得的串口接受的优先级的问题，将接收中断的优先级调到最高得到了缓解，不过还是没能解决问题。最后把定时器的优先级做了调整才解决。（待寻找到底是什么问题...)

# PWM输出问题

在测试PWM的时候不知道为什么在 ```TIM_Compare(Timx,val)``` 调节占空比时, val 为低电平， period-val 为高电平。后来发现和输出极性有关。

{% highlight c %}
TIM_OCInitStructure.TIM_OCPolarity=TIM_OCPolarity_High;
{% endhighlight %}

如上调节输出极性为高。

{% highlight c %}
TIM_OCInitStructure.TIM_OCMode=TIM_OCMode_PWM1;
{% endhighlight %}

选择定时器模式：TIM脉宽调制模式1。即向上计数时，一旦TIMx_CNT<TIMx_CCR1时通道1为有效电平，否则为无效电平。



> 参考资料：  
[STM32多通道AD采样DMA传输](www.jianshu.com/p/7ee23bb2cb65)  
[PWM TIM_OCMode](http://blog.csdn.net/gtkknd/article/details/39296151)
