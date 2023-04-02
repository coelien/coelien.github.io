---
title: 论文阅读笔记：“Deformable Convolutional Networks”
tags: 
- 可变形卷积
- architecture design
- 自适应感受野
categories:
- 论文阅读
- 图像分类
---

# Deformable Convolutional Networks

## 引言分析

cnn对于集合变换的建模主要来自，丰富的数据增强，大的模型容量以及一些简单的手工模块(max pooling)。CNN 不能对大的未知的变换进行建模。CNN模块的几何结构是固定的，卷积单元在固定的位置采样输入特征图。**自适应感受野**对视觉识别比较重要。论文提出了可变形卷积，能够大大增强CNN对集合变换的建模能力。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201546282.png" alt="image-20220920154640208" style="zoom:50%;" />

## 核心亮点

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201525872.png" alt="image-20220920152538762" style="zoom:50%;" />

**可变性卷积**

如上图所示，可变形卷积可允许采样网格的自由变换。偏移量是通过之前的特征图学习得到的。

网格R：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201531979.png" alt="image-20220920153139954" style="zoom:50%;" />

标准卷积：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201534121.png" alt="image-20220920153457095" style="zoom:50%;" />

可变形卷积：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201535564.png" alt="image-20220920153530542" style="zoom:50%;" />

因为$\Delta p_n$经常为分数，因此可以使用双线性插值：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201537591.png" alt="image-20220920153730571" style="zoom:50%;" />

其中G(q,p)为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201538286.png" alt="image-20220920153812261" style="zoom:50%;" />

只对少部分q来说，G(q,p)是非零的。

可变形卷积的过程如下图所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201541686.png" alt="image-20220920154112646" style="zoom:50%;" />

## 实验分析

**Deformable Convolution的效果**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201605974.png" alt="image-20220920160512918" style="zoom:50%;" />

**卷积核感受野**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201609089.png" alt="image-20220920160902047" style="zoom:50%;" />

- 感受野大小与物体大小有关
- 背景区域的识别需要较大的感受野

**与空洞卷积的比较**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201611674.png" alt="image-20220920161154619" style="zoom:50%;" />

**模型运行时和复杂度**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209201613844.png" alt="image-20220920161318802" style="zoom:50%;" />