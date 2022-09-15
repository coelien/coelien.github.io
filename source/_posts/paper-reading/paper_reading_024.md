---
title: 论文阅读笔记：“Convolutional Two-Stream Network Fusion for Video Action Recognition”
tags: 
- 3D conv & pooling
- fusion method
categories:
- 论文阅读
- 行为识别
---

# Convolutional Two-Stream Network Fusion for Video Action Recognition

> 这篇文章贵在思路清晰，整篇文章的问题引入及脉络在引言就已经很精彩了，很值得学习借鉴

这篇文章引言其实就是再说，当前领域比起image领域并没有较大成就，原因可能在于数据集过于noisy或者是模型能力不足（提出了一些猜想），之后说之前提出的**双流架构还有诸多问题**，一是无法将表观的位置线索同时间线索相互匹配，并且无法对这些线索沿时间的变化进行表示（或者说时间感受野太小）。然后作者就在下文依次探索了如何在考虑位置的情况下去融合两个网络，在哪里融合这两个网络以及如何在时间维度进行融合。

## 核心亮点

**空间融合 Spatial fusion 方法搜索**

- 加法融合

- 最大值融合
- 连接融合

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207011613912.png" alt="image-20220701161333889" style="zoom:50%;" />

- 卷积融合
- 双线性融合

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207011614066.png" alt="image-20220701161429043" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206291620177.png" alt="image-20220629162054151" style="zoom: 67%;" />

- 逐点乘积融合

这些方法都很直观，对比在下文的实验部分有讲到

## 网络结构

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206291642491.png" alt="image-20220629164224384" style="zoom: 50%;" />

这个网络结构的输入为T个帧，帧间隔为t，时间感受野从之前的L=20扩展为现在的T*L。 融合这些特征图的方法为3D conv 加 3D pooling，但是融合后并不截取光流这一路，因为保留它的效果更好：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207011628933.png" alt="image-20220701162812910" style="zoom:50%;" />

## 实验结论

**不同融合方法的比较**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207011539197.png" alt="image-20220701153934162" style="zoom:50%;" />

从图中可以看出，简单的sum进行融合效果就已经很不错了，使用conv来融合两个网络效果更好。

**在哪个位置进行融合的比较**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207011541255.png" alt="image-20220701154132210" style="zoom:50%;" />

从图中可以看出，越晚融合效果越好，并且不涉及较早layer的多层融合的效果最好：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207011544440.png" alt="image-20220701154427411" style="zoom:50%;" />

缺点是这种方法会近似增加一倍的参数量，有点得不偿失。

**不同时序融合方法的比较**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207011545689.png" alt="image-20220701154541657" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207011538900.png" alt="image-20220701153818844" style="zoom:50%;" />

由表可得，3D卷积融合+ 3Dpooling效果最好。
