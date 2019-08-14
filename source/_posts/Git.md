---
title: Git学习记录
categories: code
---
# Git学习记录

## 获取与创建项目
### git init
<!-- more -->
用 `git init` 在本地新建Git仓，例如：
```
    $ mkdir new  
    $ cd new/  
    $ git init  
    Initialized empty Git repository in /home/qnb/new/.git/  
```
可以在这里看到生成的 `,git` 子目录
```
qnb@qnb-pc:~/new$ ls -a
.  ..  .git
```
### Git clone
从github上拉取项目
```
git clone [url]
```
***
## 基本快照（提交）
### Git add
`git add` 可以将文件添加到缓存，例如添加 `README`
```
$ touch README
$ git status -s #查看项目的当前状态
?? README
```
接下来使用 `git add`命令添加文件
```
git add README
```
此时查看状态
```
$ git status -s
A  README
```
此时修改 `README`
```
$ echo qnb >> README #覆盖写入是>,结尾写入是>>  
$ cat README
qnb
$ git status -s 
AM README
```
其中 `AM` 状态是说明在将其添加入缓存之后又有改动，此时再次提交：
```
$ git add README 
$ git status -s 
A  README
```
### git status
显示 ***在上一次修改结束之后*** 是否有修改其中 `-s` 是为了显示简短输出，若不带参数：
```
$ git status 
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   README

```
输出较为完整
### git diff
执行 `Git diff` 以获得更为详细的 `git status`信息  
`Git diff` 能显示已经写入缓存的数据与未写入缓存的数据之间的差异，主要的应用：  
- 尚未缓存的改动（应该是尚未`add`的内容，但实际测试出现了所有的改动）：`add diff`
- 查看已改动的缓存：`git diff --cached`
- 查看已缓存的与未的缓存所有改动：`git diff HEAD`
- 显示摘要而非整个diff：`git diff stat`
```
$ echo QNB >> README 
$ cat README 
qnb
qnb
QNB
$ git diff
diff --git a/README b/README
index 7a3b31e..bdbcbd0 100644
--- a/README
+++ b/README
@@ -1 +1,3 @@
 qnb
+qnb
+QNB
$ git diff --cached
diff --git a/README b/README
new file mode 100644
index 0000000..7a3b31e
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+qnb
```
### git commit  
将 `add` 到缓存区的修改 `commit` 到仓库中  
`Git` 为你的每一个提交都记录你的名字与电子邮箱地址，故而需要登记邮箱和用户名：
```
$ git config --global user.name 'qnb'
$ git config --global user.email ‘emil@qq.com’
```
接下来进行提交
```
$ git commit -m '第一次提交' # -m 选项进行添加注释
[master (root-commit) 94b92eb] 第一次提交
 1 file changed, 1 insertion(+)
 create mode 100644 README
```
此时，显示为上一次修改之后没有修改
```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   README

no changes added to commit (use "git add" and/or "git commit -a")
```
### git reset HEAD
用于取消已缓存的内容（add的内容），例如：  
```
$ git reset HEAD README 
$ echo 测试修改撤销 >> README 
$ cat README 
qnb
qnb
QNB
测试修改撤销
$ git add README 
$ git status -s
M  README
$ git reset HEAD README
Unstaged changes after reset:
M       README
$ git status -s
 <font color=#FF0000 face="黑体">M</font> README
```
此时提交将不提交 `README` ，如果仍要提交可以使用 `-am` 参数进行 `commit`  
### 移除与移动
要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除，此时使用`rm`
```
git rm -rf <file> # 参数r为递归，f为提高权限，和系统的rm同理
```
也可以不删除工作区文件，只取消对他的跟踪（删除在缓存里的文件）
```
$ git rm --cached README 
```
`mv`功能类似，不做叙述
## 版本管理
