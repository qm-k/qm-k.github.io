---
title: Git分支保存hexo博客源码
date: 2019-08-15 16:43:08
tags:
- code
categories:
- 编程
- hexo
---
# 前言
由于hexo生成的静态资源要求保存在github的master分支上，那么源代码如果再开启一个仓存储就会很麻烦，所以如果使用分支 `branch` 将源代码和和静态资源保存在同一个仓中就会很方便。
<!--more-->
# 新建一个本地仓并推送到github分支
初始化完一个hexo目录之后（假如为HEXO）：  
```
$ cd HEXO
$ git init # 初始化本地仓
$ git remote add origin git@github.com:username/username.github.io.git	#关联远程服务器
$ git add .
$ git commit -m "blog"
$ git push origin master:remotebranch # remotebranch为远程仓的分支名，例如hexo
```
远程仓中就会有一个新的分支，备份工作完成  
# 后续
之后再做修改可以在github的 `repository setting` 中设置默认分支为hexo源码的分支，因为主要关心的也是hexo源码，设置好之后再次修改可以直接git，操作如下：
```
$ git clone git@github.com:qm-k/qm-k.github.io.git
$ git branch -a #查看分支信息
$ git checkout -b hexo origin/hexo #如果不在源码分支，则进行切换，同时创建本地hexo分支
$ npm install hexo //win10装完的时候，power shell无法识别可以再尝试npm install -g hexo-cli  
//-g为全局生效，直接安装在系统中
$ hexo init 
$ npm install
$ npm install hexo-deployer-git //快捷部署到git插件

$ npm install hexo-generator-search --save //本地搜索插件  

$ npm install --save hexo-pdf //上传pdf插件

$ npm install hexo-neat --save //压缩博客
```
    PS: nodejs是可以直接是用apt安装的，如果是win10则可以直接在官网下载安装包在安装过程中选中x86_64即可。

    hexo-cli和hexo是不同的，他是一个命令行工具，用以初始化，例如init，而hexo则用以支持在初始化完成之后的工作。

    只要保证git和nodejs存在，以上步骤就可以完成
至此迁移工作完成可以进行部署
```
$ hexo clean #如果出错进行清除尝试
$ hexo g
$ hexo s --debughexo server -p 5000 #本地显示，正常为4000端口，如果占用可以用5000
$ hexo d #确认无误可以部署
```