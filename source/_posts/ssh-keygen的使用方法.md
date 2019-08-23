---
title: ssh-keygen的使用方法
date: 2019-08-23 15:48:09
tags:
- code
categories:
- 编程
- Git
---
# 前言
为了让两个Linux机器之间使用ssh而不使用密码，采用数字签名RSA和DSA（两种加密方式）来完成
# 具体流程，A登录B
1. 登录A机
2. ssh-keygen -t [rsa|dsa],将会生成公钥和私钥，id_rsa和id_rsa.pub或id_dsa和id_dsa.pub（分别基于两种加密方式）
3. 将.pub文件复制到B机器的.ssh目录，并`cat id_rsa.pub >> ~/.ssh/authorized_keys`
4. 从A机登录B机不再需要密码，可以直接运行`ssh 192.-.-.-`,此时也可以修改`/etc/host`文件，连id也不用输入，只输入用户名

# 设置文件权限
设置`authorized_keys`文件权限为600
```
sudo chmod 600 authorized_keys
```
设置`.ssh`目录权限为700
```
sudo chmod 700 -r .ssh
```
要保证`authorized_keys、.ssh`都只有用户自己有写权限才可以，否则数字签名不支持