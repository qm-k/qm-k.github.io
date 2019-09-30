---
title: ROS的安装melodic版本
date: 2019-09-29 22:41:50
tags:
- ROS系列教程
categories:
- 学习
- ROS
---
ROS对系统版本要求极其严格，基本上每一个Ubuntu的LTS（长期支持版）都会有一个ROS的版本与之对应，具体[如下](http://wiki.ros.org/Distributions)  
<!--more-->
{%asset_img distro.jpg distro%}  

打开install的[地址](http://wiki.ros.org/melodic/Installation),选择你使用的操作系统，win10我试了一次，感觉还是在Linux上方便，感兴趣可以自己试一下。  
{%asset_img select.jpg select%}  

进去之后有很完整的安装教程，如果你不想看，而且你的系统是Ubuntu或者是Ubuntu系列的什么系统，你可以直接跟着下边的步骤操作就行。  
# 添加ROS的软件源
## 国外的软件源
网速好，能连外网可以直接：
```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```
## 国内的软件源
如果你的网速不好，使用国内的镜像源：  
- 中科大源
```
sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.ustc.edu.cn/ros/ubuntu/ $DISTRIB_CODENAME main" > /etc/apt/sources.list.d/ros-latest.list'
```
- 清华源
```
sudo sh -c '. /etc/lsb-release && echo "deb http://mirrors.tuna.tsinghua.edu.cn/ros/ubuntu/ $DISTRIB_CODENAME main" > /etc/apt/sources.list.d/ros-latest.list'
```
# 设置密钥
```
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```
如果使用了代理服务（就是科学上网），那就用下边的命令：  
```
curl -sSL 'http://keyserver.ubuntu.com/pks/lookup?op=get&search=0xC1CF6E31E6BADE8868B172B4F42ED6FBAB17C654' | sudo apt-key add -
```
{%asset_img 添加密钥.jpg 添加密钥%}
# 更新并安装
## 更新软件源
```
sudo apt update
```
更新软件源。
## 安装
之后进行安装，现在嵌入式的板子性能都跟上来了，硬盘也基本都16g起步了，直接装完整版的就可以了,附带各种仿真和可视化插件。  
```
sudo apt install ros-melodic-desktop-full
```
{%asset_img 安装.jpg 安装%}
## 安装其他的ROS组件
如果需要单独安装ROS的其他包可以尝试：
```
sudo apt install ros-melodic-PACKAGE
```
PACKAGE替换为要安装的包名就可以。  

要查看都有什么组件是可以用的可以通过：
```
apt search ros-melodic
```
以上操作如果是其他版本的ROS替换其中的melodic为对应的ROS版本即可。  
# 初始化和安装依赖
```
sudo rosdep init
rosdep update
```

# 环境配置
Ubuntu一般是使用`~/.bashrc`文件进行设置环境变量的：
```
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
# 测试
在终端运行`roscore`,如果没有报错就说明安装没问题。  
  
到这里应该绝大部分功能包都能直接用了，如果有什么功能没有可以单独去搜。