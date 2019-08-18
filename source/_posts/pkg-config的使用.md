---
title: pkg-config的使用
date: 2019-08-18 15:30:55
tags:
- code
categories:
- 编程
- 编译工具
---
# 前言
`pkg-config` 简单来说就是向用户向程序提供相应库的路径、版本号等信息的程序。
<!--more-->
例如一下常用的命令
```
$ pkg-config --libs --cflags opencv
$ pkg-config --libs --cflags gstreamer-1.0 

```
会显示一下信息
```
-I/usr/include/opencv -lopencv_shape -lopencv_stitching -lopencv_superres -lopencv_videostab -lopencv_aruco -lopencv_bgsegm -lopencv_bioinspired -lopencv_ccalib -lopencv_datasets -lopencv_dpm -lopencv_face -lopencv_freetype -lopencv_fuzzy -lopencv_hdf -lopencv_line_descriptor -lopencv_optflow -lopencv_video -lopencv_plot -lopencv_reg -lopencv_saliency -lopencv_stereo -lopencv_structured_light -lopencv_phase_unwrapping -lopencv_rgbd -lopencv_viz -lopencv_surface_matching -lopencv_text -lopencv_ximgproc -lopencv_calib3d -lopencv_features2d -lopencv_flann -lopencv_xobjdetect -lopencv_objdetect -lopencv_ml -lopencv_xphoto -lopencv_highgui -lopencv_videoio -lopencv_imgcodecs -lopencv_photo -lopencv_imgproc -lopencv_core 

-I/usr/include/gstreamer-1.0 -I/usr/include/glib-2.0 -I/usr/lib/x86_64-linux-gnu/glib-2.0/include -pthread -lgstreamer-1.0 -lgobject-2.0 -lglib-2.0  
```
就是gcc在编译时需要的库路径`-l`与头文件路径`-I`，configure的作用也就是要检查你的包并提供信息给编译工具。  
而以上的这些信息又是从哪里获取的呢？他是从包名为 `xxx.pc` 这个文件中找到的，比如以上的两条命令，他就是从`opencv.pc`、`gstreamer-1.0.pc`中找到的  

## pkg-config的工作方式
缺省情况下，`pkg-config`首先在`prefix/lib/pkgconfig/`中查找相关包（譬如opencv）对应的相应的文件（opencv.pc）。在linux上上述路径名为 `/usr/lib/pkconfig/`。若是没有找到，它也会到`PKG_CONFIG_PATH`这个环境变量所指定的路径下去找。若是没有找到，它就会报错，例如：
```
$ pkg-config --libs --cflags gstreamer
Package gstreamer was not found in the pkg-config search path.
Perhaps you should add the directory containing `gstreamer.pc'
to the PKG_CONFIG_PATH environment variable
Package 'gstreamer', required by 'world', not found
```
设置环境变量PKG_CONFIG_PATH方法举例如下：
```
export PKG_CONFIG_PATH=/opencv/lib:$PKG_CONFIG_PATH
```
输出一个.pc文件：
```
$ cat  /usr/lib/pkgconfig/libopenni.pc 
prefix=/usr
exec_prefix=${prefix}
libdir=${exec_prefix}/lib
includedir=${prefix}/include/ni

Name: OpenNI
Description: A general purpose driver for all OpenNI cameras.
Version: 1.5.4.0
Cflags: -I${includedir}
Libs: -L${libdir} -lOpenNI
```
可见.pc文件 是对其的库文件路径，头文件路径，版本号，Cflags等一些参数进行封装。  

同样也可以用一下方式对对应的库进行查询：
```
$  pkg-config opencv --libs --cflags
-I/usr/include/opencv -lopencv_shape -lopencv_stitching -lopencv_superres -lopencv_videostab -lopencv_aruco -lopencv_bgsegm -lopencv_bioinspired -lopencv_ccalib -lopencv_datasets -lopencv_dpm -lopencv_face -lopencv_freetype -lopencv_fuzzy -lopencv_hdf -lopencv_line_descriptor -lopencv_optflow -lopencv_video -lopencv_plot -lopencv_reg -lopencv_saliency -lopencv_stereo -lopencv_structured_light -lopencv_phase_unwrapping -lopencv_rgbd -lopencv_viz -lopencv_surface_matching -lopencv_text -lopencv_ximgproc -lopencv_calib3d -lopencv_features2d -lopencv_flann -lopencv_xobjdetect -lopencv_objdetect -lopencv_ml -lopencv_xphoto -lopencv_highgui -lopencv_videoio -lopencv_imgcodecs -lopencv_photo -lopencv_imgproc -lopencv_core 
```
在具体的使用中可以这样：
```
gcc basic-tutorial-1.c -o basic-tutorial-1 `pkg-config --cflags --libs gstreamer-1.0`
```
使用反引号将命令括起来，似乎使用`$()`也可以但是还没有试过，以后有机会再试。