---
title: git添加tag
copyright: true
date: 2019-09-05 20:23:57
tags: 
- code
categories:
- Git
---
# 前言
在发布软件时会加一个tag标注版本号，比如opencv可以通过tag转成对应的版本，tag会记录对应版本的commit号，所以在回溯版本时会非常的方便。
<!--more-->
# 常用命令
## 列出所有tag
```
git tag
```
加上`-l`可以进行过滤，效果类似`grep`
```
git tag -l "4.0*"
```
## 新建命令
```
git tag 1.0
```
可以新建一个叫`1.0`的tag，加上参数`-a`可以创建带备注的tag，备注信息由`-m`指定，未指定`-m`则系统会提示输入。
```
git tag -a tagname -m "备注信息"
```
## 查看tag详细信息
```
git show tagname
```
查看对应的tag详细信息，此时会打出commit号，方便回溯。
## 同步tag到远程服务器
`git push origin [tagname]`推送单个分支  
{%asset_img 推送tag.png %}  
`git push origin --tags`推送所有分支
## 切换到指定tag
可以直接切换，但是切换之后处于游离状态，不属于任何分支（branch），故此时可以直接基于此tag新建一个分支。
## 删除tag
- 本地删除  
        
      git tag -d [tagname]
- 远端删除
  
      git push origin :refs/tags/<tagname>