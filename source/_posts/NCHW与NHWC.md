---
title: NCHW与NHWC
date: 2019-10-14 14:30:03
tags:
categories:
- 学习
- 算法
---
在使用NPU的过程中会遇到要将npu输出的一维数据重新`reshape`成正常的表示图像大小、通道数、以及图片数量的数据格式，这时候有两种常用的顺序可以使用`NCHW`、`NHWC`。  
<!--more-->
| N | 一个batch内图片的数量 |
| :----: | :----: |
| C | 通道数channel。例如灰度图像为1， 彩色RGB图像为3 |
| H | 垂直高度方向的像素个数 |
| W | 水平宽度方向的像素个数 |

# 改变维度顺序
例如现在有一个张量如下:
```
x = [[[  1,   2],
      [  3,   4]],
     [[ 5,  6],
      [ 7,  8]],
     [[9, 10,
      [11, 12]]]
```
此时他的shape即为（3，2，2），可以通过`x[0,:,:]`将第一个维度的值输出出来
```
>>> x[0, :, :]
<tf.Tensor: id=7, shape=(2, 2), dtype=int32, numpy=
array([[1, 2],
       [3, 4]], dtype=int32)>
```
为什莫要讨论其中某一个维度的位置呢？这个会在下边两种存储方式优劣的对比上体现出来，主要会在访存上体现出差别。  

如果需要切换通道顺序可以使用`tf.transpose`如果是numpy变量可以使用`np.transpose`  
```
y = tf.transpose(x, [1, 2, 0]) #第一个是参数要切换顺序的张量，第二个参数是改变后的通道顺序  
#数字是原始张量中的维度编号，如原来的第一维0维现在是最后一维
```
# 将输出的一维结果转换为所需维度
各种加速棒或者npu、kpu计算的结果是一维的张量，需要将结果转化为正常的维度才能使用  
```
output = output.reshape(1, 57, 46, 46)
```
比如使用RK的1808NPU进行加速：
```
    frame = cv2.resize(frame, (inWidth, inHeight), interpolation=cv2.INTER_CUBIC)
    if not hasFrame:
        cv2.waitKey()
        break
    frameWidth = frame.shape[1]
    frameHeight = frame.shape[0]

    # input mode转为’nchw’
    frame_input = np.transpose(frame, [2, 0, 1])
    t = time.time()
    [output] = rknn.inference(inputs=[frame_input], data_format="nchw")
    print("time:", time.time()-t)
   
    # rknn输出的数组转为1x57x46x46的矩阵
    output = output.reshape(1, 57, 46, 46)
```
# 两种存储方式的优劣对比  

{%asset_img all.png%}  
由于数据存储是按张量最深层的维度进行依次遍历的就像下图这样存储：
{%asset_img diff.png%}  
在NCHW的存储方式下，每一张图片都由W通道开始存储，也就是从一行的最左端存储到最右端，之后H通道加1，存储下一行，直到整个R通道存储完毕，通道数加1，存储G通道，一直存储到这张图片结束（为了方便，每一个通道我都只画了2x2个像素），而NHWC则先遍历存储通道，将一个图片坐标下的RGB通道遍历存储完之后再存储下一个坐标上的三个通道，两种存储方式都是最后再遍历一个batch内的图片张数（将前一张全部存储完毕后存储下一张）。  
