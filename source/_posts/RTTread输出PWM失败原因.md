---
title: RTThread输出PWM失败原因
date: 2019-10-27 13:42:06
tags:
- 嵌入式搬砖手册
categories:
- 学习
- 嵌入式
- RTOS
---
在STM32F407上移植PWM输出时，遇到了无法正常输出波形的问题，RT-Thread版本为4.0.2。  
<!--more-->
经过筛查发现是由于定时器时钟没有开启导致的，需要在初始化PWM设备时对其挂载的总线时钟进行初始化。  
{%asset_img Snipaste_2019-10-27_13-49-30.jpg%}
打开`drv_pwm.c`文件，在PWM初始化中添加时钟初始化。  
```
	#ifdef BSP_USING_PWM2
	    __HAL_RCC_TIM2_CLK_ENABLE();
	#endif
```
添加完是这个样子的：  
{%asset_img Snipaste_2019-10-27_13-51-57.jpg%}  
如果需要手工添加BSP可以参考：  
[STM32上使用PWM](https://www.rt-thread.org/document/site/application-note/driver/pwm/an0037-rtthread-driver-pwm/)
[PWM设备](https://www.rt-thread.org/document/site/programming-manual/device/pwm/pwm/)