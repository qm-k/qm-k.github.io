---
title: CubeMX之GPIO
date: 2019-10-25 10:28:37
tags:
- 嵌入式搬砖手册
categories:
- 学习
- 嵌入式
- CubeMX
---
使能一个GPIO，并点亮第一个LED。
<!--more-->
1. 打开cubemx，并新建一个cubemx工程：
{%asset_img Snipaste_2019-10-25_10-31-05.png%}  
{%asset_img Snipaste_2019-10-25_10-41-19.png%}  
2. 使能外部高速晶振  
选择晶体/陶瓷 谐振器，和震荡器的区别是这个是无源的。
{%asset_img Snipaste_2019-10-25_10-54-26.png%}  
3. 打开淘宝随便买一块板子并索要原理图  
一般c8t6的板子都在10元到20元左右，打开原理图，找到板子上的灯接在哪个引脚上。
{%asset_img Snipaste_2019-10-25_10-48-10.png%}  
像我手里随便找了个板子，原理图上显示led接在PB12端口上，设置PB12引脚为输出模式，直接点击要设置的引脚就能直接选择工作模式。
{%asset_img Snipaste_2019-10-25_10-56-27.png%}  
4. 配置时钟树
   {%asset_img Snipaste_2019-10-25_11-01-00.png %}   
   没错的话会变成这样：  
   {%asset_img Snipaste_2019-10-25_11-01-34.png%}  
5. 设置GPIO的属性
   因为led要输出的，要配置为低速推挽输出模式，既不上拉也不下拉，所以可以保持默认的模式不用配置。  
   {%asset_img Snipaste_2019-10-25_11-02-52.png%}  
6. 设置工程输出属性
   {%asset_img Snipaste_2019-10-25_11-05-56.png%}  
   设置代码生成的方式
   {%asset_img Snipaste_2019-10-25_11-09-04.png%}  
7. 生成并打开代码工程
   {%asset_img Snipaste_2019-10-25_11-11-18.png%}  
   在main.c中while(1)中最后面的USER CODE BEGIN 3和USER CODE END 3中间添加如下内容。
（用户代码要加在USER CODE BEGIN N和USER CODE END N之间，否则下次重新生成代码后，会被删除）
```c
		HAL_GPIO_WritePin(GPIOB, GPIO_PIN_12, GPIO_PIN_RESET);//led pin reset
		HAL_Delay(1000);//This function provides minimum delay (in milliseconds) based on variable incremented.
		HAL_GPIO_WritePin(GPIOB, GPIO_PIN_12, GPIO_PIN_SET);//led pin set
		HAL_Delay(1000);//This function provides minimum delay (in milliseconds) basedon variable incremented.
```
之后选择编译下载就可以看到led周期为1S进行闪烁了。