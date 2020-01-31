---
title: git添加公钥
date: 2019-08-23 16:04:46
tags:
- code
categories:
- Git
---
# 前言
给github添加公钥，以防止每次push都需要输入账户密码。
<!--more-->
# 查看是否已有公钥
进入`~/.ssh`目录如果有的话可以用`ll`或者`ls -la`（l为显示权限详细信息，a是显示包括隐藏文件，`ll`同`-la`效果）查看
```
$ ls -ls ~/.ssh/
total 4
4 -rw-r--r-- 1 melrose_legend melrose_legend 884 8月  23 15:46 known_hosts
```
PS:在win10的powershell中`.ssh`路径是一致的，`~/`路径在win10上对应为`C:\Users\username\`
# 生成新的SSH KEY
输入`ssh-keygen -t rsa -C “email@email.com”`其中`“email@email.com”`为github账号,之后一路回车，效果如下： 
{% asset_img 2019-08-23_16-31.png  %}   
# 登录Github，点击setting添加公钥
添加公钥  
{%asset_img 2019-08-23_17-41.png 添加公钥%}  
输入`.pub`的内容，同时为此公钥设置一个名字，方便记忆（防止那一天忘了这是哪一台机器的公钥了）  
{% asset_img 2019-08-23_17-43.png  %}  
# 测试是否成功
    ssh -T git@github.com
如下图即为配置成功：  
{% asset_img 2019-08-23_17-48.png  %} 