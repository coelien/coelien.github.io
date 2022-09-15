---
title: 论文阅读笔记：“A Closer Look at Spatiotemporal Convolutions for Action Recognition”
tags: 
- 3D model
- r(2+1)d
categories:
- 论文阅读
- 行为识别
---

# A Closer Look at Spatiotemporal Convolutions for Action Recognition

## 核心亮点

**时空卷积 new form spatio-temporal convolution**

- **mixed convolution**

**3D卷积在网络的早些层**，2D卷积在网络的后面的层

- **(2+1)D 卷积块**

与P3D类似，这篇文章也是将3D卷积分解为一个2D空间卷积和一个1D时间卷积。这样做的**潜在好处**为：

1. 减少了参数量，更容易训练和优化
2. 激活函数增加了一倍，网络更容易表示复杂的函数

**与P3D的区别**

R(2+1)D只使用了单一类型的块，并且并不包含瓶颈设计，但是通过对（分解）维度的仔细选择（P3D只说了分解，但没说怎么对维度分解），它较P3D 的精度提高了9.1%，并且相较于152层的P3D，R(2+1)D只有34层。



<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207121032241.png" alt="image-20220712103159144" style="zoom:50%;" />

**R(2+1)D卷积块**

它将$N_i$个大小为$N_{i-1}*t*d*d$3D卷积核分解为$M_i$个大小为$N_{i-1}*1*d*d$的2D卷积核核$N_i$个大小为$M_i*t*1*1$的时间卷积核。因此$M_i$作为决定中间子空间（信号从空间卷积到时间卷积的投影）维度的超参。那么这个$M_i$该如何选择呢？

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207121045890.png" alt="image-20220712104556868" style="zoom:50%;" />

这样设计之后，参数数量是基本一致的，分解的过程如下图：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131326906.png" alt="image-20220713132651835" style="zoom:50%;" />

## 实验结果

**对比多个不同的网络架构**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131446481.png" alt="image-20220713144603435" style="zoom:50%;" />

**精度与模型复杂度的关系**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131450799.png" alt="image-20220713145002760" style="zoom:50%;" />

**精度与输入帧数的关系**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131454109.png" alt="image-20220713145418069" style="zoom:50%;" />

既然明确了精度和输入帧数之间存在trade-off，我们应该如何对其进行权衡呢？

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131505041.png" alt="image-20220713150536006" style="zoom:50%;" />

论文发现，在较短输入进行训练，再在较长帧上进行finetune会比较好。

> 使用64个gpu，真的说明目前的网络真的不好训练，并且还有值得优化的空间

**与state-of-arts的比较**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131459212.png" alt="image-20220713145907165" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131512551.png" alt="image-20220713151236510" style="zoom:50%;" />

**迁移学习**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131515178.png" alt="image-20220713151552129" style="zoom:50%;" />

