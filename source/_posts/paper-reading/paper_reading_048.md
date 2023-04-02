---
title: 论文阅读笔记：“Progressive Relation Learning for Group Activity Recognition”
tags: 
- Group Activity Recognition
- CIB
- 时空共时依赖
categories:
- 论文阅读
- 群体行为识别
---

# Convolutional Relational Machine for Group Activity Recognition (CVPR 2019)

## 引言分析

该论文所采用的方法没有显示地检测或跟踪任何个体。它提出了模型CRM，并在该模型中引入了基于行为的中间表示activity map。它是一种提取个体之间空间联系的方法。 此外论文在训练时使用了多阶段的方法来逐步优化activity map，以学习到一个预测错误率更小的activity map。最终优化过的activity map和图像或视频特征进行结合，来进行组行为的预测。

## 核心亮点

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209281256257.png" alt="image-20220928125630152" style="zoom:50%;" />

**Activity Map**

行为图实际上是2D域表示的集合（域可以理解为通道）。其2D域的个数是个体行为和组行为类别个数和。每个域实际上表示一个类别，其域表示的计算需要考虑图像中的bounding box。对于每个个体m，其个体行为为i，其组行为为g，我们会定义一个如下的2D高斯概率密度函数：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209271021978.png" alt="image-20220927102117896" style="zoom:50%;" />

通过该函数我们可以计算个体m的行为图的域i和域NI+g。一个双变量高斯图会在bounding box 上进行计算。最终所有的个体行为图会进行对齐，即对于每个域的同一个位置，我们取最大值来得到最终的activity map A。对于一个单独的输入来说，其组行为是确定的，即只有一个组行为类别的域非零，其他域均为0。

**Refinement**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209281328374.png" alt="image-20220928132852350" style="zoom:50%;" />

采用如图的公式即可迭代地对activity map进行改进

**Aggregation**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209281333419.png" alt="image-20220928133338397" style="zoom:50%;" />

如上式，最终的组行为表示的计算公式即结合了特征图F和改进过的activity map。

**Training Objectives**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209281337410.png" alt="image-20220928133719382" style="zoom:50%;" />

La实际上是预测行为图和真实行为图之间的欧几里得损失，Lg是群体行为的分类交叉熵损失。

## 实验结果

**对比实验**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209281341620.png" alt="image-20220928134116594" style="zoom:50%;" />

**SOTA**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209281342909.png" alt="image-20220928134235878" style="zoom:50%;" />

**可视化**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209281348806.png" alt="image-20220928134851617" style="zoom:50%;" />