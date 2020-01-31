---
title: ubuntu-ssh无法连接时
date: 2019-09-30 17:17:41
tags:
- 奇淫巧计
categories:
- error&warning
---
新装的`Ubuntu`有可能在ssh连接时连接不上。这个时候应首先确认是否在统一局域网网段下：  
```
$ifconfig
```
<!--more-->
查看是否用于与主机连接的设备具有同一网段下的IPV4地址。  
{%asset_img ifconfig.png ifconfig%}  

如果此时没有IPV4地址，则可以使用  
```
$sudo ifconfig [DEV NAME] [IPV4 address]
```
进行IP设置。  

如果依旧无法连接，查看是否安装`openssh-seaver`,安装完再次测试。
```
sudo apt install openssh-seaver
```

在win上可以使用Xshell全家桶直接连接，在Ubuntu上则可以使用`ssh username@IP`进行连接（username改为对应的用户名，IP改为对应的ipv4地址）