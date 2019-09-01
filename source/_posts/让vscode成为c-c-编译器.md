---
title: 让vscode成为c/c++编译器
date: 2019-08-31 17:54:00
tags:
- code
categories:
- 编程
- 工具
---
# 前言
安装了vscode之后，写代码和丝滑流畅，脱离臃肿的`Visual Studio IDE`，优雅的进行断点调试，直接进行编译与运行，下边记录一下配置的过程。
<!--more-->
# 简介
vscode本身是不带编译器的，无论通过什么方式进行编译和运行都是需要外部的编译器的，只需要提前安装好对应的编译器，并设置好环境变量，vscode就可以调用到。  

vscode可以通过终端*使用命令行*进行编译，也可以借助`code runner`进行简化编译。

# 安装编译器
如果使用`code runner`进行编译运行的话是需要安装g++/gcc的，如果是Linux直接命令行安装就行，win10的话需要提前安装并设置环境变量，通常选择安装[MinGW](https://sourceforge.net/projects/mingw/files/latest/download?source=files)或者[MinGW-w64](https://sourceforge.net/projects/mingw-w64/files/mingw-w64/)，推荐使用MinGW-w64，这两个其实差不多，但是听说MinGW-w64是MinGW的升级版，更强更骚，总之装MinGW-w64就OK。  

安装MinGW-w64的过程没什莫要说的，在安装过程中注意将`arch`改为x86_64，如下图：  
{% asset_img install_mingw.PNG 修改%} 

win10的话之后要添加一下环境变量，将安装路径下的`bin`文件夹与`includ`文件夹分别加入系统环境变量的`Path`与`C_INCLUDE_PATH` ( 这个需要自己新建，也可以为c++设立一个c++的头文件搜索路径`CPLUS_INCLUDE_PATH` )  

安装目录：  
{% asset_img path.PNG 安装目录%}  
添加路径：  
{% asset_img add_path.PNG 添加路径%}

安装完成后，测试一下：
```
g++ -v
gcc -v
```
如下表示安装正常

{% asset_img successfu_linstall.PNG 安装完成%}

# 