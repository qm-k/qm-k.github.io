---
title: Kconfig的语法
date: 2019-10-13 18:22:56
tags:
categories:
- 编程
- linux
---
内核在编译的时候会遍历每一个目录下的所有的Makefile文件，来决定编译哪个文件，确定文件的依赖关系。而Makefile会根据Kconfig来生成，也就是说，Kconfig配置了哪些文件编译，哪些文件不编译。  
<!--more-->
| 文件 | 位置 | 功能 |
| :------:| :------: | :------: |
| Kconfig | 每个源码目录下 | 提供选项 |
| .config | 源码顶层目录下 | 保存选择结果 |
| Makefile | 每个源码目录下 | 根据.config中的内容来告知编译系统如何编译 |  

# 例子
Kconfig中每一个config就是一个选项，最上面跟着控制句柄，下面则是对这个选项的配置，如选项名是什么，依赖什么，选中这个后同时会选择什么。  
```
config SOC_STM32F103C8
    bool  "select SOC"
    select SOC_SERIES_STM32F1
    select RT_USING_COMPONENTS_INIT
    select RT_USING_USER_MAIN
    default y
    help
        config SOC
```
比如上边这一段,假设其目录为`bsp/SOC/.`，可以在同级目录的Makefile下能找到
```
obj-$(SOC_STM32F103C8) += xxx.o
/*
Obj-$(CONFIG_选项名) += xxx.o 
当CONFIG_选项名=y时，表示对应目录下的xxx.c将被编译进内核
当CONFIG_选项名=m时对应目录下的xxx.c将被编译成模块
*/
```
而在上一级目录`bsp/.`中就会发现有一个Makefile中有
```
obj-y           += SOC/
```
确保SOC目录下的文件能参与编译。

`bsp/.`的Kconfig中有
```
menu "SOC_select"
source "bsp/SOC/Kconfig"
```
以便上一级的Kconfig能检索到下一级的的Kconfig，在menuconfig中生成目录。
# Kconfig语法
由上边的例子不难看出，在menuconfig中更改的就是config后边句柄(SOC_STM32F103C8)的值，来确定后边的`xxx.o`是否生成，也就是说能控制`xxx.c`或`xxx.cpp`能否被编译。  
这个是他的最基本的功能，而剩下的功能列一个表出来就是这个样子的：  

| 内容 | 功能 |
| :------:| :------: |
| config | 选项 |
| SOC_STM32F103C8 | 句柄，可用于控制Makefile选择是否编译 |
| bool | TRUE选中句柄的值为y、FALSE不选句柄的值为n 选中则编译，不选中则不编译。（如果后面没有字符串名称，则表示其不会出现在选择软件列表中） |
| select  | 当前选项选中后则select后指定的选项自动被选择 |
| default | 值的默认情况 |  

```
 depends on BSP_USING_UART1 && RT_SERIAL_USING_DMA
```
depend on 依赖，后面的四个选择全被选上，这个选项才能被选
```
 depends on BSP_USING_UART1 || RT_SERIAL_USING_DMA
```
反之
后面的四个选择其中至少一个被选择，这个选项才能被选
## 当选项不是布尔型而是数字时
```
config ARM_DMA_IOMMU_ALIGNMENT
    int "Maximum PAGE_SIZE order of alignment for DMA IOMMU buffers" ---->该选项是一个整型值
    range 4 9 ---->该选项的范围值
    default 8 ---->该选项的默认值
    help
      DMA mapping framework by default aligns all buffers to the smallest
```
## 由编译的前置条件
```
if ARCH_RISC_V --->若ARCH_64选项选中了，下边的才会有机会

config CPU_k210
    bool "选项名"
    help
      Enable k210 CPU support

endif
```
## 做环境变量
```
config BSP_DIR
    string
    option env="BSP_ROOT"
    default "."
```
设置BSP_DIR的值。
## 多个选项
```
choice      --->表示选择列表
    prompt "Default I/O scheduler"         //主目录名字
    default DEFAULT_CFQ                    //默认CFQ
    help
      Select the I/O scheduler which will be used by default for all
      block devices.

    config DEFAULT_DEADLINE
        bool "Deadline" if IOSCHED_DEADLINE=y 

    config DEFAULT_CFQ
        bool "CFQ" if IOSCHED_CFQ=y

    config DEFAULT_NOOP
        bool "No-op"

endchoice
```
在这些选项中选择一个。
## menu
```
menu "Onboard Peripheral Drivers" //menu说明是不可选的菜单，其后为其菜单名

    config BSP_USING_USB_TO_USART
        bool "Enable USB TO USART (uart1)"
        select BSP_USING_UART
        select BSP_USING_UART1
        default y

endmenu ----> menu菜单结束
```
{%asset_img menu.png%}
打开menu之后：
{%asset_img select_menu.png%}
## menuconfig
```
    menuconfig BSP_USING_UART
        bool "Enable UART"
        default y
        select RT_USING_SERIAL
        if BSP_USING_UART
            config BSP_USING_UART1
                bool "Enable UART1"
                default y

            config BSP_UART1_RX_USING_DMA
                bool "Enable UART1 RX DMA"
                depends on BSP_USING_UART1 && RT_SERIAL_USING_DMA
                default n
        endif
```
只有选中了才能进入的目录。
{%asset_img menuconfig.png%}
打开后：
{%asset_img select_menu.png%}