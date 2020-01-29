---
title: ORB特征提取与匹配
mathjax: true
date: 2020-01-26 21:26:38
tags:
- 算法
- CV
categories:
- 学习
- 算法
- CV
---
提起特征点检测就绕不开ORB这个经典的检测算法，下边对ORb特征识别进行简单的叙述。
<!--more-->
# ORB原理
ORB特征是将FAST特征点的检测方法与BRIEF特征描述子综合起来，先通过FAST特征点检测图像上存在的可用特征点，检测到之后在特征点附近的像素上通过BRIEF特征描述算子对特征点进行描述，在匹配阶段通过BRIEF特征描述算子计算得到的特征向量进行相似度判断进行匹配。

# 特征点定义
图像特征点可以认为是图像中比较特殊的点，例如：阴影、高光、轮廓等等，在图像上`不平坦`的点，表现在图像矩阵上就是梯度高的像素，这样的一类像素点可以用来作为图像的特征进行识别和检测。  
# 特征点的检测（FAST）
ORB采用FAST（features from accelerated segment test）算法来检测特征点。这个定义基于特征点周围的图像灰度值，检测候选特征点周围一圈的像素值，如果候选点周围领域内有足够多的像素点与该候选点的灰度值差别够大，则认为该候选点为一个特征点。  
$$
N = \sum_{ x \forall{circle(p)} } \left| I(x) - I(p) \right| > \varepsilon_d
$$
其中$I(x)$为圆周上任意一点的灰度，$I(p)$为圆心的灰度，$\varepsilon_d$为灰度值差得阈值，如果N大于给定阈值，一般为周围圆圈点的四分之三，则认为点p是一个特征点。  
{%asset_img FAST_9.png FAST%}  
为了获得更快的结果，还采用了额外的加速办法。如果测试了候选点周围每隔90度角的4个点，应该至少有3个和候选点的灰度值差足够大，否则则不用再计算其他点，直接认为该候选点不是特征点。候选点周围的圆的选取半径是一个很重要的参数，这里为了简单高效，采用半径为3，共有16个周边像素需要比较。为了提高比较的效率，通常只使用N个周边像素来比较，也就是大家经常说的FAST-N。很多文献推荐FAST-9，作者的主页上有FAST-9、FAST-10、FAST-11、FAST-12，大家使用比较多的是FAST-9和FAST-12。  
# 计算特征描述子（BRIEF）
得到特征点后我们需要以某种方式描述这些特征点的属性。这些属性的输出我们称之为该特征点的描述子（Feature DescritorS）.ORB采用BRIEF算法来计算一个特征点的描述子。  
BRIEF算法的核心思想是在关键点P的周围以一定模式选取N个点对，把这N个点对的比较结果组合起来作为描述子。
在选取点对时有多种方法，设我们在特征点的邻域块大小为S×S内选择n个点对(p,q)：
- 在图像块内平均采样；
- p和q都符合(0,125S2)的高斯分布；
- p符合(0,125S2)的高斯分布，而q符合(0,1100S2)的高斯分布；
- 在空间量化极坐标下的离散位置随机采样
- 把p固定为(0,0)，q在周围平均采样
{%asset_img RRIEF_5.png RRIEF5种情况%}  

假设我们选到的点对的流程为：
1. 以关键点P为圆心，以d为半径做圆O。
2. 在圆O内某一模式选取的N个点对。这里为方便说明，N=4，实际应用中N可以取很高。  
{%asset_img RRIEF.png RRIEF得到的4对算子%}  
3. 定义四个点对为：
$$
P_1(A,B) ,P_2(A,B) ,P_3(A,B) ,P_4(A,B)
$$
4. 定义T运算：
$$
T_i(P_i(A,B))=
\begin{cases}
1\qquad I_A > I_B\\\\
0\qquad I_B \leq I_B
\end{cases}
$$
5. 对图中的四个点对进行T运算，并组合成一个向量：
$$
T_1 (P_1(A,B))=1\\\\
T_2 (P_2(A,B))=0\\\\
T_3 (P_3(A,B))=1\\\\
T_4 (P_4(A,B))=1
$$
可得最终的描述子为$T=1101$
## 理想的特征点描述子应该具备的属性
人在观察一张图像的时候是不关心图像的摆放、明暗、大小的，人眼都可以准确的根据图像的特征对内容进行判断，相应的我们的描述子也应该有这样的特性，应该具有尺度一致性、旋转一致性等。  
在在OpenCV中的ORB使用了图像金字塔实现了尺度一致性，下边来解决一下旋转一致性的问题。  
## 旋转一致性
在当前关键点P周围以一定模式选取N个点对，组合这N个点对的T操作的结果就为最终的描述子。当我们选取点对的时候，是以当前关键点为原点，以水平方向为X轴，以垂直方向为Y轴建立坐标系。当图片发生旋转时，坐标系不变，同样的取点模式取出来的点却不一样，计算得到的描述子也不一样，这是不符合我们要求的。因此我们需要重新建立坐标系，使新的坐标系可以跟随图片的旋转而旋转。这样我们以相同的取点模式取出来的点将具有一致性。  

简而言之，就是让**提取关键点的坐标系能够跟随图像旋转同时旋转**（如下图），这样就可以保证在图像旋转前后提取的数据一致。  
{%asset_img 旋转一致性.png 旋转一致性%}  
而要实现旋转一致性，只要找到"质心"即可，因为质心是固定的，会随物体转动而转动。  
我们设Q为重心，我们以$P \rightarrow Q$为x轴，以P为原点，这样旋转时坐标就可以跟着旋转，在不同的旋转角度下，我们以同一取点模式取出来的点就是一致的。  

由高中物理可知，质量密度不均匀的物体质心坐标公式为：  
二维
$$
\begin{aligned}
    \overline{x} = \frac{1}{M} \iint_D x \mu (x,y) d \sigma = \frac{\iint_D x \mu (x,y) d \sigma }{\iint_D \mu (x,y) d \sigma}  \\\\
    \overline{y} = \frac{1}{M} \iint_D y \mu (x,y) d \sigma = \frac{\iint_D y \mu (x,y) d \sigma }{\iint_D \mu (x,y) d \sigma}  
\end{aligned}
$$
三维也同理，但是类比到图像上只需要二维即可，将像素的像素值作为其质量：
$$
M = \sum_{X=-R}^{R} \sum_{Y=-R}^{R} I(x,y)\\\\
M_{\overline{x}} = \sum_{X=-R}^{R} \sum_{Y=-R}^{R} xI(x,y)\\\\
M_{\overline{y}} = \sum_{X=-R}^{R} \sum_{Y=-R}^{R} yI(x,y)\\\\
Q_X = \frac{M_{\overline{x}}}{M},Q_Y = \frac{M_{\overline{y}}}{M} \qquad \text{X坐标，Y坐标}
$$
可以得到“质心”的坐标和角度
$$
O = \left( \frac{M_{\overline{x}}}{M} , \frac{M_{\overline{y}}}{M} \right) \qquad \text{坐标}\\\\
\theta = atan2( \frac{M_{\overline{x}}}{M_{\overline{y}}} ) \qquad \text{与x轴正向夹角}
$$
## 滤波
BRIEF中，采用了9x9的高斯算子进行滤波，可以一定程度上解决噪声敏感问题，但是单纯的一个滤波是不够的，在ORB中我们通过对生成的点对附近的图像进行积分代替对应点的灰度值。  
$$
I(A) = \sum_{x=-r}^{r} \sum_{y=-r}^{r} I(x,y)
$$
# 匹配
计算两个特征点描述子之间的相似度即可，例如特征点A、B：  
A = 1001110110  
B = 0110110010  
共计10位，其中5位相同，所以此时的相似度为50%，此时我们如果设置一个阈值即可判断A、B是否为匹配的。
## 汉明距离
两个等长字符串之间的汉明距离是两个字符串对应位置的不同字符的个数。
```python
def hammingDistance(s1, s2):
    """Return the Hamming distance between equal-length sequences"""
    if len(s1) != len(s2):
        raise ValueError("Undefined for sequences of unequal length")
    return sum(el1 != el2 for el1, el2 in zip(s1, s2))
```

```c++
class Solution {
    public int hammingDistance(int x, int y) {
        int i = x ^ y;
        int count = 0;
        while (i != 0) {
            if ((i & 1) == 1) {
                count++;
            }
            i = i >> 1;
        }
        return count;
    }
}
```