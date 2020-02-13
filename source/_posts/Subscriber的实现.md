---
title: Subscriber的实现
mathjax: false
date: 2020-02-13 13:36:41
tags:
- ROS系列教程
categories:
- 学习
- ROS
---
接上一篇publish的实现，如果有操作不明白可以翻之前的文章。
<!--more-->
{%asset_img Snipaste_2020-02-13_14-08-23.png rqt_graph%}  

# 创建新包
过程参考前一节，我们创建一个名为`pose_subscriber`的包，同时创建同名的可执行程序。  
类似单片机的中断机制，一旦满足条件跳入指定的函数，这就是订阅者Subscriber。
# 代码
```
#include <ros/ros.h>
#include "turtlesim/Pose.h"
void poseCallback(const turtlesim::Pose::ConstPtr& msg)
{
	ROS_INFO("Turtle pose :x:%0.6f y:%0.6f",msg->x ,msg->y);
}
int main(int argc ,char **argv)
{
	//初始话节点，节点名为Pose_subscriber
	ros::init(argc ,argv ,"Pose_subscriber");
	//创建句柄
	ros::NodeHandle n;
    //创建Subscriber，定义从/turtle1/pose话题进行接收，同时绑定回调函数为poseCallback，队列长度为10
	ros::Subscriber pose_sub = n.subscribe("/turtle1/pose",10,poseCallback);
    //进入死循环进行等待，一旦subscribe指定的话题有数据，则触发绑定的回调
	ros::spin();
}
```
整个程序就是接受小乌龟的坐标信息，运行之前那个发布速度的节点之后，就可以看到实时返回变化的坐标值，同时计算图会有三个节点连接在一起。  
这时候如果取消roscore的话会发生什么呢？什么都不会发生，因为ros master是用来辅助topic连接用的，一旦连接成功，他就没用了，关掉rosmaster节点也可以保证topic的正常使用。  
## CMakelist.txt
经过两节个实例的使用，这个时候会注意到CMakelist.txt中有很多注释，CMakelist是一个很常用的管理C++工程的东西，单独的叙述后边有空再整理，但是这里这些注释是很有用的，他里边写了一些常规CMakelist中没有的东西。  
总结ros生成的CMakelist.txt中包含的就是以下几个东西：
- 必需的CMake版本：cmake_minimum_required()
- 软件包名：project()
- 查找编译依赖的其他CMake/Catkin包（声明依赖库）：find_package()
- 启动Python模块支持：catkin_python_package()
- 消息/服务/操作(Message/Service/Action)生成器：add_message_files(),add_service_files(),add_action_files()
- 调用消息/服务/操作生成：generate_messages()
- 指定包编译信息导出：catkin_package()
- 添加要编译的库和可执行文件：add_library()/add_executable()/target_link_libraries()
- 测试编译：catkin_add_gtest()
- 安装规则：install()
其中的生成器在下边的自定义消息中会使用。