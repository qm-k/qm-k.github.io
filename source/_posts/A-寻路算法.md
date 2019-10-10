---
title: A*寻路算法
date: 2019-10-07 22:42:15
tags:
- ROS
- 导航
categories:
- 学习
- 算法
---
在导航算法的入门当中经常会学的一个入门算法就是A\*算法，这里整理一下对A\*算法的理解。
<!--more-->
A\*算法本质上就是一个BSF的贪心算法，以下边这个地图为例。  
{%asset_img map.png map%}
完整的A*算法要考虑到斜着移动等等更复杂的问题，这里先暂时不考虑，只考虑最基本的情况。
在整个算法的过程中总共涉及了两个集合三个参数，分别为[Closelist]、[Openlist]、F、G、H。  
# 变量说明
- Openlist:可达到的格子的集合  

- Closelist：已到达的格子  

- F = G + H 对格子价值的评估  

- G代表从起点到现在格子的距离（成本）  
- H代表不考虑障碍时当前格子到达终点的距离  

最终的目的就是使F的值最小。  
# 算法实现过程
就以上边的地图为例，总共包括三步：  
- Step1：  
将可到达的节点放入Openlist链表。  
{%asset_img Step1.png 放入起点%}  
此时可到达起点，先将起点的坐标作为一个节点放入Openlist链表。  
- Step2：  
找出Openlist中F值最小的方格，同时将节点移入Closelist，表示已经检查过这个点（能到达该位置）。  
由于现在只有起始点，所以将起始点放入Closelist。
{%asset_img Step2.png 放入起始点%}  
- Step3  
找到所有能到达的格子，检查是否已经在Openlist中，如果不在就加入Openlist，计算Openlist中每个节点的F值，并把当前的节点作为父节点。  
{%asset_img Step3.png %}  

之后不停的循环Step2与Step3，直到终点出现在Openlist中则说明寻路结束，否则说明不可到达。  

# 完整示例
接上边三步。  
- Round2
  现在Openlist中F值最小的节点是Node(2,2),此时将Node(2,2)作为*当前方格*，并将该节点从Openlist移入Closelist中，表示他已经检擦过了，并加入路径中。  
  {%asset_img Round2.png %}  
- Round3
  现在将所有能到达的节点全部找出，并计算其F值，新的能到达的节点的父节点为当前方格。  
  {%asset_img Round3.png%}  

  然后不断迭代，即可得到最短路径，下边将迭代过程展示出来，不再仔细叙述其中每一步的计算.  

其中金色的为加入到Closelist的节点，浅蓝色的为可到达（Openlist）节点，绿色为当前节点，其父子关系从检索开始到检索结束都不可更改，根据父子关系从目标节点回溯到起始节点。  
{%asset_img 1.png %}  
{%asset_img 2.png %}  
{%asset_img 3.png %}  
{%asset_img 4.png %}  
{%asset_img 5.png %}  
{%asset_img 6.png %}  
{%asset_img 7.png %}  
{%asset_img 8.png %}  
{%asset_img 9.png %}  
{%asset_img 10.png %}  
{%asset_img 11.png %}  


至此结束，最终的Closelist应该是一个树状链表，其中到达目标节点的即为所求。