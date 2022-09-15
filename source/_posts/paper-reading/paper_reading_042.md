---
title: 论文阅读笔记：“ Spatial Transformer Networks”
tags: 
- input transformer
- localization network
- grid generator
categories:
- 论文阅读
- 图像分类
---

# Spatial Transformer Networks

## 引言分析

CNN不具备在网络的浅层或是中间层对于输入数据的空间不变性，论文提出了一种新的模块spatial transformer，该模块不仅会帮助我们选择图像中最相关的区域，同时还能将这些区域变换为经典的、预期的姿态来简化在后续层中的识别。如下图b所示，定位网络可以预测使用何种变换；c图是应用了变换后，transformer的输出。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091104920.png" alt="image-20220909110438865" style="zoom:50%;" />

## 核心亮点

**空间变换网络 Spatial Transformer Network**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091113647.png" alt="image-20220909111317599" style="zoom:50%;" />

Spatial Transformer Network包含了三个组件：定位网络和参数化网格采样机制（网格生成器）和图像采样器。在CNN中加入该模块可以帮助最小化总体的网络花费函数。关于如何变换每个训练样本被压缩或缓存到了定位网络中的权重参数中。

**定位网络 localization network**

$\theta=f_{loc}(U)$

其输入是特征图U，输出是参数$\theta$，其大小取决于变换的类型。例如仿射变换就是6维的。定位函数$f_{loc}$可以使用任何的形式，如全连接或是卷积，并且应当包含一个回归层来生成参数$\theta$。

**参数化采样网格**（网格生成器）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091412480.png" alt="image-20220909141216426" style="zoom:50%;" />、

为了对输入特征图进行变形，每个输出像素都是由在输入特征图的特定位置应用一个采样kernel来计算的

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091431963.png" alt="image-20220909143142932" style="zoom:50%;" />

$(x^t_i,y_i^t)$为输出特征图中正则网格的目标坐标。$(x^s_i,y_i^s)$为输入特征图中对应的源坐标。

**可微分图像采样**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091429605.png" alt="image-20220909142949578" style="zoom:50%;" />

通过上式可以计算每个通道中每一位置的输出值。因为采样函数的不连续性，次梯度(sub-gradients)必须被使用。

## 实验设计

**Distorted MNIST**

> 探索网络可以学习到的多种变换

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131036201.png" alt="image-20220913103610094" style="zoom:50%;" />

如图使用TPS变换+CNN+ST的效果最好

**街景房号识别**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131058050.png" alt="image-20220913105820995" style="zoom:50%;" />

**细粒度识别**

![image-20220913110643589](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131106708.png)

由右图，不同的transformer识别不同的部位