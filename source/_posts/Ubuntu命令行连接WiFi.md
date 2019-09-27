---
title: Ubuntu命令行连接WiFi
date: 2019-09-27 20:56:40
tags:
categories:
- 编程
- Linux
---
使用NetworkManager工具进行网络管理，命令行中使用的命令是`nmcli`，其功能有一下常用的几项：
* 查看网络设备列表
```
$ sudo nmcil dev
```
如果设备状态是`unmanaged`的，说明网络设备不受NetworkManager管理，你需要清空`/etc/network/interfaces`下的网络设置，然后重启
* 开启WiFi
```
$sudo nmcli r wifi on
```
* 扫描附近的WiFi
```
$sudo nmcli dev wifi
```
* 连接到指定的WiFi热点
```
$sudo nmcli dev wifi connect [SSID] password [PASSWORD] ifname [DEVICE NAME]
```
将SSID、PASSWORD与DEVICE　NAME分别改为实际的WiFi名称、密码与设备名即可连接成功。