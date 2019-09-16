---
title: ROS的工程目录
copyright: 
date: 2019-09-08 12:29:57
tags:
- ROS
categories:
- 学习
- ROS
---
	
<blockquote class="blockquote-center">优秀的人，不是不合群，而是他们合群的人里面没有你</blockquote>

# 整体架构
按照官方的Wiki来看，ROS整体是由文件系统级、计算图级、社区级三个层级，这里先来看一下他的工程目录结构。
<!--more-->
```mermaid
graph LR;
node1-->|message/subscribe|topic1;
node1-->|server/response|node3
node3-->|server/request|node1;
node1-->|message/publish|topic2;
node3-->|message/publish|topic3;
topic3-->|message/subscribe|node5;
topic2-->|message/subscribe|node2;
topic2-->|message/subscribe|node4;
node2-->|message/subscribe|topic1;
subgraph NODE1
node1 
node3 
end
subgraph topic
topic1
topic2
topic3
end
subgraph NODE2
node2
node4
node5
end
```
# 文件系统
其不同功能的组件是放在不同的目录下的，目录是按照功能进行分类的。
```mermaid
graph LR;
work_space-->src
work_space-->build
work_space-->

```