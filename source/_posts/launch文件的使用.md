---
title: launch文件的使用
mathjax: false
date: 2020-03-18 20:08:13
tags:
- ROS系列教程
categories:
- 学习
- ROS
---
# 简介
在运行一个ROS工程的时候经常要同时打开很多的node，这个时候需要开很多的终端而且在调试参数时非常的麻烦，为了解决这个问题ROS提供了一个roslaunch的机制，方便同时运行多个nodes。  
<!--more-->
launch文件是由XML格式写成的，遵循XML的书写规范。  
# 使用
启动一个launch文件
```
$ roslaunch package_name launch_file_name
```
或者：
```
$ roslaunch completely_path
```
launch文件是不需要必须包含在package中的，可以直接用绝对路径启动，或者在所在路径下直接`roslauch launch_file_name`  
# 内容
先举一个完整的例子
```xml
<launch>
    <arg name="model" />
    <arg name="gui" default="false" />

    <param name="robot_description" textfile="$(find autolabor_description)/urdf/autolabor_pro1.urdf" />
    <param name="use_gui" value="$(arg gui)" />

    <!-- 场景模拟 -->
    <node pkg="map_server" type="map_server" name="map_server" args="$(find simulation_launch)/map/amcl_map.yaml" />

    <node name="simulation_stage" pkg="autolabor_simulation_stage" type="simulation_stage_node" output="screen">
	<param name="input_topic" value="map" />
	<param name="output_topic" value="stage_map" />
	<param name="real_map_frame" value="real_map" />
    </node>

    <node pkg="map_server" type="map_server" name="map_server" args="$(find simulation_launch)/map/amcl_map.yaml" />
    <node name="joint_state_publisher" pkg="joint_state_publisher" type="joint_state_publisher" />
    <node name="robot_state_publisher" pkg="robot_state_publisher" type="state_publisher" />
    <node name="rviz" pkg="rviz" type="rviz" args="-d $(find simulation_launch)/rviz/amcl_simulation.rviz" />

    <node pkg="tf" type="static_transform_publisher" name="base_link_to_laser" args="0.0 0.0 0.20 0.0 0.0 0.0 /base_link /lidar 10" />
</launch>
```
其中对ROS的roslaunch文件参数展示的不是特别完整，完整的roslaunch文件包括下边几种元素：
- launch：launch文件的根元素
- node：用于启动节点
- param：设置参数服务器上的参数（参数服务器在param的小节中说明，有空再写）
- machine：声明用于工作的设备，可以是本地的也可以是远程的
- includ：声明包含的其他的launch文件
- env：指定启动节点的环境变量
- test：启动一个测试节点
- arg：声明参数
- env：
- remap：用于声明重映射，例如两个话题的数据类型相同，一个只有订阅者一个只有发布者，这个时候想将发布的内容放到订阅的话题里时可以使用（有空再详写）
- rosparam：
- param：定义参数服务器上的参数
或许我叙述的不完整，常用的就是这些。  

## launch
launch文件是由XML语法构成的，所以会有一个根。  
launch文件的根元素采用`<launch>`标签。  
```xml
<launch>
    <!--more-->
</launch>
```
## node
用于描述要启动的节点。  
下边举个具体的例子：
```xml
<node pkg="package-name" type="executable-name" name="node-name" args="arg1 arg2 args.yaml" machine="machine-name"/>
```
- pkg:节点所在的功能包的名字
- type：节点的可执行程序的名字（在Cmakelist中定义的编译目标）
- name：节点运行时的名字
- args：传递到节点的参数
- machine：启动节点的指定机器，这里可以是SSH链接的远程服务器,会在machine标签中单独定义

举个特殊例子说明一下特殊应用：
```xml
<node pkg="package-name" type="executable-name" name="node-name" args="--test" respawn="true" respawn_delay="10" required="true" />
```
- args="--test"：带有这个参数启动时，会去尝试加载，如果加载失败会自动重新加载。
- respawn="true"：节点退出时自动重启
- respawn_delay="10"：在respawn="true"的情况下，如果需要重启会等待respawn_delay秒之后再重启
- required="true"：如果节点死掉了，就杀掉所有roslaunch

# 参数
## 多窗口启动
在需要在启动的某些节点中使用键盘做输入的情况下，我们不能让所有的信息在一个terminal中做输出，这个时候可以加参数`launch-prefix=”command-prefix”`使得每一个节点在单独的terminal中输出
