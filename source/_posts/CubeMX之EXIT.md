---
title: CubeMX之EXIT
date: 2019-11-02 14:10:05
tags:
- 嵌入式搬砖手册
categories:
- 学习
- 嵌入式
- CubeMX
---
使能一个中断，并通过按键控制LED亮灭。
<!--more-->
这一章在[CubeMX之GPIO](https://www.qm-k.xyz/2019/10/25/CubeMX%E4%B9%8BGPIO/)的基础上进行操作，复制整个工程之后，打开`.ico`（cubemx的工程文件）文件。  
查看原理图，这里以我自己板子上的Key0为例，将PB9配置为GPIO_EXIT0模式。
Snipaste_2019-11-02_14-11-24.jpg 引脚定义  
Snipaste_2019-11-02_14-15-57.jpg 配置PB9为GPIO_EXIT0模式  
我这块板子设计比较不合理，按键没有做任何上下拉处理，如果是一个正常的板子这个K0应该是有上拉的，按键按下为低电平，松开为高电平，不过现在这样也不是不能用，于是