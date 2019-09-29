---
title: ROS的工程目录
copyright: 
date: 2019-09-08 12:29:57
tags:
- ROS系列教程
categories:
- 学习
- ROS
---
	
<blockquote class="blockquote-center">优秀的人，不是不合群，而是他们合群的人里面没有你</blockquote>

# 整体架构
按照官方的[Wiki](http://wiki.ros.org/cn/ROS/Tutorials)来看，ROS整体是由文件系统级、计算图级、社区级三个层级，这里先来看一下他的工程目录结构,我的叙述难免会有失偏颇，可以参考官方的[Wiki](http://wiki.ros.org/cn/ROS/Tutorials)进行理解。
<!--more-->
## 文件系统级  
{%asset_img ROS文件系统架构.jpg 文件架构%}  
其不同功能的组件是放在不同的目录下的，目录是按照功能进行分类的。  
1. 工作空间：  
   工作空间是一个包含功能包、编译包、可执行程序的文件夹，类似于vsIDE里新建的一个工程目录，可以在一个工程中做多个功能包，也可以做多个工作空间把功能分开，按自己的习惯来就好，一般第一个工作空间都会新建成catkin_ws，初始化完成后目录下会有三个文件夹：src、build、devel。  
   * **src源工作空间**：这个文件夹用于存放功能包和一个用于配置这些功能包的cmake文件CMakeLists.txt。由于ROS中的源码采用catkin工具进行编译，虽然每一个版本的catkin工具会有一些变化，但总体都是基于cmake的，所以在src源文件空间和各个功能包中都会有一个CMakeLists.txt文件，这个文件就是起编译配置的作用。
   * **build编译文件目录**：用于存放CMake和catkin编译功能包时产生的缓存、配置、中间文件等。
   * **devel执行文件目录**：编译好的可执行程序，可以不用安装直接运行的二进制文件，可以通过直接导出这些文件与其他人共享开发的成果。
2. packages  
   一个功能包具有用于创建ROS程序的最小结构和最少内容，它可以包含ROS运行的进程（节点）、配置文件等。
   * **CMakeLists.txt功能包配置文件**：用于编译时的配置文件。
   * **package.xml功能包清单文件**：用xml的标签格式标记这个功能包的各类相关信息，比如包的名称、***依赖***关系等。主要作用是为了更容易的***安装依赖***和分发功能包。`rosdep install`就是根据这里的信息进行安装的！！！
   * **src功能包源文件**：具体的任务实现，各进程各节点程序的源文件存放目录。主要需要关注和开发的目录。
   * msg非标准消息定义：如果ROS提供的标准消息定义的数据类型不能满足需求可以在这里另外定义，非必须目录。
   * srv非标准服务定义：如果ROS提供的标准服务定义的数据类型不能满足需求可以在这里另外定义，非必须目录。
   * scripts可执行脚本文件存放目录：各种可执行脚本存放的地方，可以支持bash、python、js等等，但用不着就不用整这个目录了。
   * launch文件目录：多个节点（线程）同时启动时可以通过在`.launch`文件中列出来可执行程序及参数，然后只启动一个`*.launch`文件就可以启动整个工程，同样的，需要这个功能就加，不需要就不用添加这个目录。  
   * ROS提供了很多用于增删改查功能包的快捷工具，有一些常用的：  
    rospack find [package name]：用于获取信息或在系统中查找工作空间。  

        catkin_create_pkg：用于在工作空间的src源空间下创建一个新的功能包。  

        catkin_make：用于编译工作空间中的功能包。  

        rosdep install [package]：用于安装packages的所需的系统依赖项，详情可看[Wiki](http://wiki.ros.org/cn/ROS/Tutorials/rosdep)。  

        rqt_dep：用于查看功能包的依赖关系图。

        等等还有很多，入门常用的都在[这里](http://wiki.ros.org/cn/ROS/Tutorials)。  
## 计算图级  
