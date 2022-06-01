---
title: 论文阅读笔记：“Deep Temporal Linear Encoding Networks”
tags: 
- 时间线性编码
- TLE Layer
categories:
- 论文阅读
- 行为识别
---
# Deep Temporal Linear Encoding Networks

## 一. 论文思想

这篇文章主要是针对如何对整个视频进行表示，即如何对视频中的片段进行聚合，提出了时间线性编码（TLE），它可以嵌入在CNN中作为一个层，从而允许端到端的训练。

## 二. TLE　Layer

<img src="C:\Users\sixwa\AppData\Roaming\Typora\typora-user-images\image-20220331170312907.png" alt="image-20220331170312907" style="zoom:50%;" />

TLE层主要分为两个部分，第一个是对视频的多个片段的特征图进行聚合的操作，第二个是将聚合表示转化为线性编码特征的编码操作，算法如下图所示：

<img src="C:\Users\sixwa\AppData\Roaming\Typora\typora-user-images\image-20220331171227702.png" alt="image-20220331171227702" style="zoom: 50%;" />

聚合方法可采用（逐元素）最大聚合，平均聚合和乘积聚合，编码方法可采用双线性模型和全连接池化。实验表明，双线性模型较第二种方法计算更加快速，参数量更少的同时效果更好，使用Tensor Sketch算法也可以通过将高维空间投影到低维空间而无需直接计算外积。作者在已有的典型模型的基础上只保留卷积，通过加入TLE层来进行实验。实验证明使用该层对模型效果均有较大提升。值得一提的是，作者还将环境场景信息纳入考虑，作为额外信息进行聚合，实验表明准确率有较大提升，该实验证明了TLE合并不同数据流的能力。

<img src="C:\Users\sixwa\AppData\Roaming\Typora\typora-user-images\image-20220331173204667.png" alt="image-20220331173204667" style="zoom:50%;" />
