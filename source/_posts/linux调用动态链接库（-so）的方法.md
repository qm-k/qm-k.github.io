---
title: linux调用动态链接库（.so）的方法
date: 2019-08-24 19:14:36
tags:
- code
categories:
- 编程
- 工具
---
动态链接库在编译的时候不会编译进去，所以需要制定系统对动态链接库的搜索路径，`-L`(`l`与`L`区别暂时还不清楚，应该`l`同理)添加的搜索路径仅用于编译，所以如果搜索不到动态链接库则会在运行时报错。
<!--more-->
1. 在`/etc/ld.so.conf.d/`下创建`xxx.conf`(xxx为库名例如opencv.conf)，在文本中加入`.so`所在路径，如：`/usr/lib`、`/usr/xxx`等等，使用`sudo ldconfig`命令使之生效。
2. 将`.so`所在路径添加为`LD_LIBRARY_PATH`环境变量。
3. 在编译命令中使用`-Wl,-rpath=./`参数，并将相应`.so`拷贝到执行目录（未使用过）；  