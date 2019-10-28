---
title: CubeMX的安装
date: 2019-10-25 08:58:52
tags:
- 嵌入式搬砖手册
categories:
- 学习
- 嵌入式
- CubeMX
---
意法半导体的一系列软件工具让嵌入式工程师变成了大白菜，只需要点点点就能把硬件配置完成，让用户只关注于软件逻辑，再加上越来越成熟的HAL库，软件在不同硬件上的区别越来越小，可扩展性和可移植性都越来越强。下边从CubeMX的安装开始，逐步讲解STM系列的使用。
<!--more-->
现在ST公司升级和维护的库主要就是STM32CubeMX的HAL库和标准外设库，而STM32CubeMX是一个配置STM32代码的工具，它把很多东西封装的比较好，硬件抽象层（HAL）、中间层、示例代码等。  

整个软件非常现代化，有很直观的资源提示，在配置冲突的情况下有很直观的错误信息，可视化的端口配置与时钟树配置，对快速上手并理解单片机非常友好。  

登录ST[官网](https://www.st.com/content/st_com/en.html),在产品展示（Products）中直接搜索CubeMX，可以搜到STM8的，还有STM32的，根据需要选择，找不到的话直接点[这里](https://www.st.com/content/st_com/en/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-configurators-and-code-generators/stm32cubemx.html)进行登录，然后点`Get Software`填一下个人邮箱信息，会把下载链接发到邮箱。  
{%asset_img Getsoftware.png%}  
因为CubeMX是基于Java的，所以还需要安装一下Java，安装[链接](https://www.java.com/zh_CN/download/win10.jsp),下载后一直点确定就行，可能安装的时候会自动下载东西，网差的话多试几次。  
{%asset_img Java.png%}  

安装好之后还需要安装使用的芯片对应的芯片包。这个可以在软件内进行安装也可以在网站上单独下载安装包进行安装，一般在软件内安装更方便一些，但是对网络要求更高一点，需要科学上网，具体过程如下：
- 打开CUBEMX，打开包管理器：
{%asset_img manage.png%}  
- 选择要用的芯片：  
{%asset_img pack1.png%}  
{%asset_img pack2.png%}  

安装好后就可以开始工程了。  
所有过程中的路径一定不要有中文，如果遇到无法生成代码的问题，很大可能就是中文路径或者是权限不足导致的。