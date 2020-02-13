---
title: Publisher的实现
mathjax: false
date: 2020-02-10 16:43:57
tags:
- ROS系列教程
categories:
- 学习
- ROS
---
ros中我认为最重要的一点就是他构建了一套统一的通信机制,将硬件给隐藏掉,使得开发者可以专注于算法的研究.
<!--more-->
# 话题(topic)
topic:  
    话题是一个很常用的机制,如果学过操作系统,很容易就会把他和队列联系起来,先进先出,有固定长度.
    而用户不必关心他的具体实现,只需要按照固定的方式创建所需数据类型的topic,然后将需要发送的标准数据使用系统提供的方法进行发送,接收的节点用系统提供的方法进行接收,即可完成数据的交换.所以只要是系统提供的标准数据或是用户定义过的数据,就可以很轻松的使用一些集成好的算法包进行测试.
# 发布(publish)
这里以一个具体的例子来讲:
{%asset_img Snipaste_2020-02-12_17-56-19.png rqt_graph%}
其中有两个node,一个topic,这里使用[工作空间于功能包](https://www.qm-k.xyz/2020/02/09/%E5%B7%A5%E4%BD%9C%E7%A9%BA%E9%97%B4%E4%B8%8E%E5%8A%9F%E8%83%BD%E5%8C%85/)中创建的包进行测试.  
ros自带的小乌龟节点`rosrun turtlesim turtlesim_node`会根据`/turtle1/cmd_vel`话题的内容进行移动,所以我们自己写的publisher向该话题发布速度信息,能使得小乌龟动起来就说明我们的publish正常工作了.  
# 涉及的操作
|操作|命令|
|:---:|:---:|
|编译|$catkin_make <br> #只能在工程的根目录下进行，就是在上文的catkin_ws中|
|启动小乌龟的节点|$rosrun turtlesim turtlesim_node|
|启动自己的节点|$rosrun <包名> <节点名> <br> 包名是创建时的命令指定的也就是src目录下的文件夹名，可以进行多级嵌套以最深处的为准 <br> 节点名则为编译源文件时在CMakelist.txt中指定的可执行程序名，或为python等脚本语言的脚本名<br>*注意：这个和系统运行时计算图中的节点不是一个东西，计算图中的节点是程序中使用ros::init()进行定义的*|
|图形化显示当前的节点关系|$rosrun rqt_graph rqt_graph |
|显示当前的话题列表|$rostopic list |
|生效环境变量|$source devel/setup.bash|
  

# 代码
要实现一个发布者需要四个环节：
1. 初始化ROS节点
2. 向ROS Master注册节点信息包括发布的话题名和话题中的消息类型
3. 创建消息数据
4. 按照一定的频率进行发送
```
#include <ros/ros.h>
#include <geometry_msgs/Twist.h>
int main(int argc ,char **argv )
{
    //1. ROS节点初始化，定义计算图的节点名为vel_public
    ros::init(argc,argv,"vel_public");
    //创建节点句柄
    ros::NodeHandle n;
    //2. 新建发布者，设定消息类型为Twist，向/turtle1/vmd_vel话题发送消息，队列长度设为10
    ros::Publisher turtle_vel_pub = n.advertise<geometry_msgs::Twist>("/turtle1/cmd_vel",10);
    //设定循环周期，可以确定loop_rate.sleep()的时间
    ros::Rate loop_rate(10);

    while(ros::ok())
    {
        //3. 初始化消息类型为twist
        geometry_msgs::Twist vel_msg;
        vel_msg.linear.x = 5 ; 
        vel_msg.angular.z = 2 ; 
        //根据publisher初始话的设置进行发布
        turtle_vel_pub.publish(vel_msg);
        ROS_INFO("Publisher connmend  %0.2f m/s , %0.2f rad/s",vel_msg.linear.x,vel_msg.angular.z);

        loop_rate.sleep();
}
return 0;
}
```
# 编译
c++在ROS中是使用CMake来管理的，修改包中的CMakelist.txt即可。过程很简单：
1. 设置需要生成的代码和可执行文件名（最后会生成在catkin_ws/devel/lib/包名/可执行程序，也就是节点名）：`add_executable(节点名 src/源文件)`
2. 链接需要的库：`target_link_libraries(节点名 ${catkin_LIBRARIES})`
```
add_executable(vel_publisher src/vel_publisher.cpp) #编译
target_link_libraries(vel_publisher ${catkin_LIBRARIES}) #链接
```
这些设置写在`install`的最后和`build`之前就可以,注释写的很清楚具体的书写规范和每一个命令的具体功能。
{%asset_img  Snipaste_2020-02-13_13-14-04.png %}
之后直接回到工程根目录下catkin_make就可以，编译结束使用`source devel/setup.bash`刷新环境变量让系统能找到刚编译的包。  
{%asset_img  Snipaste_2020-02-13_13-25-18.png %}  
100%即为编译成功。 
# 运行
```
$roscore #启动master节点
$rosrun turtlesim turtlesim_node #打开小乌龟节点
$rosrun 包名 节点名 #运行/devel/lib/包名/目录下的节点可执行程序
```