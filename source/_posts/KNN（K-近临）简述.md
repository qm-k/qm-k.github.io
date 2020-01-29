---
title: KNN（K-近临）简述
date: 2020-01-19 17:07:37
tags:
- 算法
categories:
- 学习
- 算法
- ML
mathjax: true
---
K-Nearest Neighbor algorithm简称KNN（K近邻算法），由名字来看就是依据原有数据之间的`距离`对新数据进行`分类`的一个算法。  
<!--more-->
# 概述
最简单的分类器可以将所有的数据对应的类别进行记录，在得到新数据时将数据的属性与已记录的数据进行匹配，当完全匹配时即可确定数据的类型。  

但是一般情况下不可能对所有可能的数据进行统计，所以这时候就出现了KNN这样基于数据统计的算法（其实绝大多数机器学习算法都是基于数据统计的）。  
# 实现过程
KNN是通过对已知的样本特征值求距离进行分类的，即得到一个样本在特征空间中与其最相似（或者说最邻近）的K个样本，则这K个样本中最多的类型就认为是所求样本的特征值。  

下面通过一个简单的例子来说明一下（又是这张图）：  
{%asset_img KNN.png%}  
由图中我们可以看出在平面（特征空间）上共计由三类样本，我们将红色三角化为一类，将蓝色方块化为一类，绿色的圆作为待分类的样本，每个样本都有两个坐标（特征值），分别为x轴坐标和y轴坐标。真实的场景中这里可以是高维的特征空间和特征向量对应分类样本中可提取的特征值。接着看这张图，根据上边所说，将有一下两种情况：  
- 若K = 3，则可以画出以绿色为圆心的第一条实线将三个最近的样本包括进来，则这时候我们则将圆划分为红色三角形的类别。  
- 同理，若K = 5，则蓝色的占3/5，这时候蓝色占多数，将圆形的样本分类为蓝色正方形。  
  

总结起来就是几个步骤：
- 求得待分类样本与数据集中所有样本的距离（一般我们用欧式距离或者曼哈顿距离）
- 将数据集中的样本按照距离由小到大排序，取前K个数据
- 统计前K个数据中各个类别出现的频率
- 频率出现最高的类别就可以认为是代求的样本的类别  
# 欧式距离与曼哈顿距离
## 欧式距离
$$ d(X,Y)= \sqrt{\sum_{k=1}^{n}{(x_i-y_i)}^2} $$  
欧式距离也称欧几里得距离，是最常见的距离度量，衡量的是多维空间中两个点之间的绝对距离。
也就是N维空间中两点间的真实距离，或向量的自然长度。  
## 曼哈顿距离
$$ d(X,Y)= \sqrt{\sum_{k=1}^{n}{|x_i-y_i|}} $$  
曼哈顿距离，也称出租车距离，也就是在各个维度上的距离差之和。曼哈顿距离也是最简单的距离计算方法。  

# K值的意义和选择
## K的意义
来了一个样本x，要给它分类，即求出它的y，就从数据集中，在x附近找离它最近的K个数据点，这K个数据点，类别c占的个数最多，就把x的label设为c  
## K值的选择
1. 如果选择较小的K值，就相当于用较小的领域中的训练实例进行预测，“学习”近似误差会减小，只有与输入实例较近或相似的训练实例才会对预测结果起作用，与此同时带来的问题是“学习”的估计误差会增大，换句话说，K值的减小就意味着整体模型变得复杂，容易发生过拟合；
2. 如果选择较大的K值，就相当于用较大领域中的训练实例进行预测，其优点是可以减少学习的估计误差，但缺点是学习的近似误差会增大。这时候，与输入实例较远（不相似的）训练实例也会对预测器作用，使预测发生错误，且K值的增大就意味着整体的模型变得简单。
3. K=N，则完全不足取，因为此时无论输入实例是什么，都只是简单的预测它属于在训练实例中最多的类，模型过于简单，忽略了训练实例中大量有用信息。  

在实际应用中，K值一般取一个比较小的数值，例如采用交叉验证法（简单来说，就是一部分样本做训练集，一部分做测试集）来选择最优的K值。


# 算法实现
python代码，在同一个目录下新建一个名为`KNN.xls`的表格，前两列作为特征值，第三列作为类别标签，之后可以直接运行脚本看到现象，或者也可以在命令行中使用方法生成样本。  
```python
#coding:utf-8

import numpy as np
import operator   #内置的算数比较包
import xlrd    #用于操作表格

##给出训练数据以及对应的类别
def createDataSet():
    group = np.array([[1.0,2.0],[1.2,0.1],[0.1,1.4],[0.3,3.5]])
    labels = ['A','A','B','B']
    return group,labels

###通过KNN进行分类
def classify(input,dataSet,label,k):
    dataSize = dataSet.shape[0]
    ####计算欧式距离
    diff = np.tile(input,(dataSize,1)) - dataSet
    sqdiff = diff ** 2
    squareDist = np.sum(sqdiff,axis = 1)###行向量分别相加，从而得到新的一个行向量
    dist = squareDist ** 0.5
    
    ##对距离进行排序
    sortedDistIndex = np.argsort(dist)##argsort()根据元素的值从大到小对元素进行排序，返回下标

    classCount={}
    for i in range(k):
        voteLabel = label[sortedDistIndex[i]]
        ###对选取的K个样本所属的类别个数进行统计
        classCount[voteLabel] = classCount.get(voteLabel,0) + 1
    ###选取出现的类别次数最多的类别
    maxCount = 0
    for key,value in classCount.items():
        if value > maxCount:
            maxCount = value
            classes = key

    return classes

if __name__ == '__main__':
    group = np.array([[0,0],[0,0],[0,0],[0,0],[0,0],[0,0]],dtype=np.float)
    labels = ['?','?','?','?','?','?']

    DataSet = xlrd.open_workbook(r'./KNN.xls')
    sheet = DataSet.sheet_by_index(0)
    print("数据共：",sheet.ncols," 列",sheet.nrows," 行")
    print(group.shape[0])
    for i in range(sheet.nrows):
        group[i] = sheet.cell_value(i,0),sheet.cell_value(i,1)
        labels[i] = sheet.cell_value(i,2)
        print("第 ",i," 行的数据为",group[i],"类别为",labels[i])

    inputdata = eval(input("please input your data ( separated by commas):"))
    K = eval(input("input K value ：")) #eval 将输入时自动添加的""去掉
    DataSet = np.array(inputdata)
    output = classify(DataSet,group,labels,K)
    print("resout of the class is : ",output)
```
具体代码可以去我GitHub上看：
[https://github.com/qm-k/Algorithm_Getting_Started](https://github.com/qm-k/Algorithm_Getting_Started)