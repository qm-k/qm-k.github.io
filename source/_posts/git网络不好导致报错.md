---
title: git网络不好导致报错
date: 2019-08-23 14:58:09
tags:
- code
categories:
- Git
---
# 前言
校园网总是自带网速debuff，这就导致经常会在下载的时候中断，这个时候有几个常用的解决办法。
<!--more-->
# 修改缓冲区大小和超时时间（这个并没有明显的效果）
```
git config --gloab http.postBuffer 524288000 #gloab为全局设置
git config --gloab http.lowspeedLimit 0
git config --gloab http.lowspeedTime 999999
```
# 自动循环下载脚本
通过`shell`脚本实现自动断线重连
```
#！ /bin/sh
repo sync
while[$? -ne 0] //$?为命令返回值，成功为0，失败为非0
do              //具体在shell部分中进行讨论
repo sync
done
```