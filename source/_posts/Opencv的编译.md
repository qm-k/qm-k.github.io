---
title: Opencv的编译WIN
copyright: true
date: 2019-09-08 16:15:03
tags:
- code
- CV
categories:
- 学习
- CV
---
# CV源码
源码肯定是要先有的，[opencv](https://github.com/opencv/opencv)、[opencv_contrib](https://github.com/opencv/opencv_contrib),推荐用`git`下载，不然版本是没办法在下载之后进行切换的。  

很明显再Linux上没有任何难度，就把Linux往后放放，先说win10
# 在win10上借助vs进行编译
## 安装cmake
win10的话直接去[官网](https://cmake.org/download/)上下载msi的安装包双击安装就行,如下，选64位的安装  
{%asset_img download.PNG 下载cmake%}  
下一步就行，可以选择把路径加入系统环境变量里。  
## 安装Visual Studio IDE
[官网](https://visualstudio.microsoft.com/zh-hans/vs/community/?rr=https%3A%2F%2Fcn.bing.com%2F)直接下载，在线安装社区版（Community），免费的！勾个扩展，勾个想要的组件点安装就完事了，想要python就勾一个，缺东西安装完成后也可以再单独添加。  
{%asset_img vs_install.PNG VS_INSTALL%}  

记得选中`Desktop development with C++`,这个应该会包含`Visual C++ tools for CMake`  
{%asset_img 解决err1.PNG 添加c++工具%}  
如果不选会出现：  
{%asset_img err1.PNG err1%}  

**同时一定要保证源码的完整性，刚开始报了各种错误都是因为源码不完整导致的。**

## 打开cmake进行配置
打开cmake图形界面：
1. 输入源码路径
2. 选择输出路径，可以自己新建一个
3. 点击config  
{%asset_img cmake.PNG cmake界面%}  

完成后添加contrib库：  
{%asset_img contrib.PNG 附加库%}  

之后搜索`build_opencv_world`,勾上，这里一定要勾上！！！
{%asset_img vuild_opencv_world.png world%}  

之后再此点击config，出现`configuring done`就可以点`generate`  
{%asset_img config_done.PNG %}  

全部通过后会有：  
{%asset_img generate.PNG }  


出现IPPICV或者ffmpeg下载失败等问题，建议尝试科学上网。
## 打开vs进行编译
进入之前新建的build目录，如果刚刚的步骤一切顺利，这里会有一个vs的解决方案文件`.sln`  
{%asset_img sln.PNG%}  
打开他，找到all_build  
{%asset_img build.PNG%}
右键生成，经过漫长的等待，，，直到：  
弹出了个错误！！  
{%asset_img err2_python.PNG%}  

### 解决LNK1104,打不开python3_d.lib
重新打开当时安装python的程序，点击修改，然后勾上`debug`选项，就像这样：  
{%asset_img err2.PNG%}  
然后安装，安装完成后给opencv_python3添加`.lib`路径  
{%asset_img err2_add_path.PNG%}  
打开属性，依次添加库目录与附加依赖项:  

1. 将`python37_d.lib`所在文件夹的路径添加到库目录（用以指定搜索`.lib`的路径）里面
{%asset_img python_lib.png%}  

2. 添加附加依赖项`python37_d.lib`  
{%asset_img err2_fujia.png%}  

**应该是如果添加了库目录，这里就可以只写文件名，反之则可以写完整路径。**

附加项中添加的路径就是刚找到的`python37.lib`的路径。
{%asset_img err2_libs.png%}  

然后重新生成opencv_python3,成功。
{%asset_img err2_well.png%}  
[参考](https://blog.csdn.net/weixin_43788499/article/details/84933210)

之后生成`INSTALL`,成功会显示：  
{%asset_img install.png%}  
然后就可以在编译好的文件就可以在install文件夹中找到了。
{%asset_img over.png%}  

# 添加环境变量
新建一个项目，随便起一个名字。  
打开下边的选项。
{%asset_img shuxingguanli.png%}  
{%asset_img dianjishuxing.png%}  

打开属性，修改下边的两项,以及附加依赖项`opencv_world412.lib`(我的cv版本是4.12的所以是412，同时这里也是为什莫上边`cmake config`一定要勾选`build_opencv_world`的原因)  

{%asset_img vs_config.png%}
1. 包含目录  
   {%asset_img baohan.png 包含目录%}
2. 库目录
   {%asset_img ku.png 库目录%}
3. 附加依赖项  
   {%asset_img fujiayilai.png 附加依赖%}
  **这里应该需要添加所有的`.lib`文件，复制下边的bat脚本在lib目录下运行就可以得到所有的文件名，记得将`.cmake`文件删掉再复制进附加依赖项**  

```bat
@echo off
DIR "./" /B >A.TXT
pause
```
之后就可以跑一个例程测试一下了。
```cpp
#include <opencv2/core.hpp>
#include <opencv2/videoio.hpp>
#include <opencv2/highgui.hpp>
#include <iostream>
#include <stdio.h>

using namespace cv;
using namespace std;

int main(int, char**)
{
	Mat frame;
	//--- INITIALIZE VIDEOCAPTURE
	VideoCapture cap;
	// open the default camera using default API
	// cap.open(0);
	// OR advance usage: select any API backend
	int deviceID = 0;             // 0 = open default camera
	int apiID = cv::CAP_ANY;      // 0 = autodetect default API
	// open selected camera using selected API
	cap.open(deviceID + apiID);
	// check if we succeeded
	if (!cap.isOpened()) {
		cerr << "ERROR! Unable to open camera\n";
		return -1;
	}

	//--- GRAB AND WRITE LOOP
	cout << "Start grabbing" << endl
		<< "Press any key to terminate" << endl;
	for (;;)
	{
		// wait for a new frame from camera and store it into 'frame'
		cap.read(frame);
		// check if we succeeded
		if (frame.empty()) {
			cerr << "ERROR! blank frame grabbed\n";
			break;
		}
		// show live and wait for a key with timeout long enough to show images
		imshow("Live", frame);
		if (waitKey(5) >= 0)
			break;
	}
	// the camera will be deinitialized automatically in VideoCapture destructor
	return 0;
}
```
正常是可以打开USB摄像头并显示的。