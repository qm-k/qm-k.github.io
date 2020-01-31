---
title: 让vscode成为c/c++编译器
date: 2019-08-31 17:54:00
tags:
- code
- 奇淫巧计
categories:
- linux
copyright: true
---
# 前言
安装了vscode之后，写代码和丝滑流畅，脱离臃肿的`Visual Studio IDE`，优雅的进行断点调试，直接进行编译与运行，下边记录一下配置的过程。
<!--more-->
# 简介
vscode本身是不带编译器的，无论通过什么方式进行编译和运行都是需要外部的编译器的，只需要提前安装好对应的编译器，并设置好环境变量，vscode就可以调用到。  

vscode可以通过终端*使用命令行*进行编译，也可以借助`code runner`进行简化编译。

# 安装编译器
如果使用`code runner`进行编译运行的话是需要安装g++/gcc的，如果是Linux直接命令行安装就行，win10的话需要提前安装并设置环境变量，通常选择安装[MinGW](https://sourceforge.net/projects/mingw/files/latest/download?source=files)或者[MinGW-w64](https://sourceforge.net/projects/mingw-w64/files/mingw-w64/)，推荐使用MinGW-w64，这两个其实差不多，但是听说MinGW-w64是MinGW的升级版，更强更骚，总之装MinGW-w64就OK。  

安装MinGW-w64的过程没什莫要说的，在安装过程中注意将`arch`改为x86_64，如下图：  
{% asset_img install_mingw.PNG 修改%} 

win10的话之后要添加一下环境变量，将安装路径下的`bin`文件夹与`includ`文件夹分别加入系统环境变量的`Path`与`C_INCLUDE_PATH` ( 这个需要自己新建，也可以为c++设立一个c++的头文件搜索路径`CPLUS_INCLUDE_PATH` )  

安装目录：  
{% asset_img path.PNG 安装目录%}  
添加路径：  
{% asset_img add_path.PNG 添加路径%}

安装完成后，测试一下：
```
g++ -v
gcc -v
```
如下表示安装正常

{% asset_img successfu_linstall.PNG 安装完成%}

# 安装插件
去左侧的插件商店中所搜一下 `C/C++`与`Code runner` 这两个插件，装上，这时候在右上角就能出现一个能直接运行的三角：  

{% asset_img run_code.PNG 运行按钮%}  

但是这时候运行不是在vscode集成的控制台里的，我们需要修改一下设置：文件>首选项>设置>用户设置>拓展>Run Code Configuration（直接搜Run Code Configuration往下翻也能找到）  
{% asset_img run_in_terminal.PNG 设置%}  
下边测试一下：
```c++
#include<bits/stdc++.h>  
using namespace std;

int main()
{
    cout<<"hello world"<<endl;
    return 0;
}
```
但是，runcode只是帮我们运行了`g++ xxx.cpp -o xxx.exe`，而不会加别的东西，也就是说，如果我们调用了opencv的库，这时候编译会出错的，因为他不会给我们加上`-lopencv`,这个时候我们就依然需要在命令行里手写g++了。

ps：`bits/stdc++.h`是一个万能c++库，他包含了基本所有常用的c++库，但是也会造成编译慢的问题，能不用还是不要用。
# 调试
要实现断点调试需要先在工作区目录下新建一个`.vscode `文件夹（win10下如果不能新建`.`开头的文件夹，重命名成` .vscode. `就可以解决问题），添加两个文件， `launch.json` 和 `tasks.json`  
将一下代码分别加入两个文件。
launch.json
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "C/C++",
            "type": "cppdbg",
            "request": "launch",
            "program": "${fileDirname}/${fileBasenameNoExtension}.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "D:/Program Files/mingw-w64/x86_64-8.1.0-posix-seh-rt_v6-rev0/mingw64/bin/gdb.exe",
            "preLaunchTask": "g++",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
        },
    ]
}
```
其中 `miDebuggerPath` 参数是用以搜索mingw安装的调试器(`gdb.exe`)的 ，所以这个路径要设置正确
ps : `externalConsole`参数为`fales` 时才是在vscode集成的命令行中进行调试。 

tasks.json
```json
{
    "version": "2.0.0",
    "command": "g++",
    "args": [
        "-g",
        "${file}",
        "-o",
        "${fileDirname}/${fileBasenameNoExtension}.exe"
    ],
    "problemMatcher": {
        "owner": "cpp",
        "fileLocation": [
            "relative",
            "${workspaceRoot}"
        ],
        "pattern": {
            "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error):\\s+(.*)$",
            "file": 1,
            "line": 2,
            "column": 3,
            "severity": 4,
            "message": 5
        }
    },
    "group": {
        "kind": "build",
        "isDefault": true
    }
}
```
此时可以按 `ctrl+shift+b` 直接调用配置好的g++ task 编译程序而不运行程序，但是要注意如果要单步运行就要有断点存在，不然在左侧的`debug`窗口中没有变量显示，也不能暂停，至少我在实际使用中是这样的情况。  
{% asset_img debug.PNG debug%}