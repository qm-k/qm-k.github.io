---
title: linux查看局域网下所有主机IP
date: 2019-10-11 14:13:52
tags:
categories:
- linux
---
在Linux下使用nmap工具对局域网扫描，可以通过查看arp缓存表就可以知道局域网内所有设备的IP和MAC地址。
<!--more-->
1. 安装nmap：  
```
$ sudo apt install nmap
```
2. 输入指令进行查询
```
nmap -sP 172.20.10.0/24
```
这条指令是以ping的方式进行扫描。  
还可以用UDP ping  
```
nmap -PU 172.20.10.0/24
```
SYN扫描
```
nmap -sS 172.20.10.0/24
```