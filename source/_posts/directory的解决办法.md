---
title: Python.h:No such file or directory的解决办法
date: 2019-09-21 14:17:00
tags:
- CV
- python
categories:
- 编程
- error&warning
---
出现Python.h:No such file or directory的错误，有两种情况，一种是没有python.h这个文件，一种是python的版本不对，可以进入/usr/includ/文件夹下的pythonx.x文件夹里看一下有没有`python.h`这个文件  
如果没有(第一种情况)，可以运行一下  
```
$ apt install python-dev
```
