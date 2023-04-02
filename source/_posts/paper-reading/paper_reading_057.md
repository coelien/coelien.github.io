---
title: 论文阅读笔记：“Spatiotemporal Multiplier Networks for Video Action Recognition”
tags: 
- Activity Recognition
categories:
- 论文阅读
- 行为识别
---

# Spatiotemporal Multiplier Networks for Video Action Recognition

## 主要思想

- to solve the **combination of two streams**:

**multiplicative motion gating（乘法运动门控）**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211250915089.png" alt="image-20221125091530994" style="zoom:50%;" />

如图2d，其公式为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211250916714.png" alt="image-20221125091658691" style="zoom:50%;" />

在反向传播过程中，运动和表观流的输入被显示地牵涉，作为其梯度上的门控机制。它使得模型有能力去学习时空特征关联

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211250930645.png" alt="image-20221125093006604" style="zoom:50%;" />

-  to solve **long-term input**

**inject temporal filters（注入时序过滤器）**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211250921553.png" alt="image-20221125092129508" style="zoom:50%;" />

对应该变换的初始化，作者令其不会改变原始特征（即恒等映射），思想其实就是进行一个3D卷积，只在通道上和时序上进行了信息处理（不涉及空间维度信息变换）。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211250931439.png" alt="image-20221125093141403" style="zoom:50%;" />