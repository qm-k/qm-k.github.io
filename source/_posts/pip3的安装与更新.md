---
title: pip3的安装与更新
mathjax: false
date: 2020-02-08 14:09:44
tags:
- 炼丹学基础
categories:
- linux
---
# 安装
<!--more-->
在Ubuntu系统上，一般会默认安装python2和python3，在阿里的云服务器上一般会附带pip2。如果需要更新python3，不要直接卸载，会导致系统崩溃，如果要安装pip3，可以直接执行：
```
sudo apt-get install python3-pip
```
{%asset_img Snipaste_2020-02-08_14-16-54.png 缺少pip3提示安装%}  
如果太慢，可以将系统镜像源换为国内的,点进链接有详细的说明。

[阿里镜像](https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.3e221b1126dWW1)  
https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.3e221b1126dWW1  
[清华镜像](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)  
https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/

# 更新
```
sudo pip3 install --upgrade pip
```
更新成功会有：
{%asset_img Snipaste_2020-02-08_14-28-34.png 缺少pip3提示安装%}  
网络不好可以临时使用清华镜像进行更新：
```
pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple pip -U
```
# 换源
更新之后
设为清华源：
```
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```
设为阿里源：
将`~/.pip/pip.conf`改为：
```
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```
# 报错
如果报了下边的错（好早的错误了，现在应该已经修复了）
{%asset_img 1-1Q002223024439.jpeg 缺少pip3提示安装%}  
可以`sudo vi /usr/bin/pip3`，打开这个文件，之后进行修改：  
```python
from pip import main
if __name__ == '__main__':
sys.exit(main())
```
修改为：
```python
from pip import __main__
if __name__ == '__main__':
sys.exit(__main__._main())
```
保存即可正常使用。