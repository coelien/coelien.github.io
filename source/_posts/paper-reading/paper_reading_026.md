---
title: 论文阅读笔记：“A Hierarchical Deep Temporal Model for Group Activity Recognition”
tags: 
- Group Activity Recognition
- LSTM
- hierachical model
categories:
- 论文阅读
- 群体行为识别
---

# A Hierarchical Deep Temporal Model for Group Activity Recognition

## 核心亮点

**层次模型**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206281222031.png" alt="image-20220628122241869" style="zoom:50%;" />

如图，该模型逻辑上分为两个阶段，第一个阶段预测单人级别的行为，第二阶段收集第一阶段获得的行为隐变量，并希望对整个场景的行为进行建模，即组行为预测（这里默认同一个场景内的所有人为一组）。

第一阶段预测单人行为识别，通过CNN提取基于图像的特征，将特征序列作为输入传给LSTM网络：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206281232146.png" alt="image-20220628123249111" style="zoom:50%;" />

其中ht为隐变量。第二个阶段，先对所有人的隐变量进行池化，再将全局信息送入LSTM网络学习组级别的动态信息，它的输出隐变量再输入softmax分类层得到分类结果：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206281237424.png" alt="image-20220628123758397" style="zoom:50%;" />

第一个公式代表连接空间特征和时序特征，第二个公式对同一帧的所有人进行max pooling。

## 实验结果

**baseline消融实验**

- 图像分类模型：使用调优的model对单帧进行组行为识别
- 组分类模型：对于单帧输入，使用model得到fc7特征，并对其进行池化后，送入softmax分类器来进行组行为识别
- 调优组分类模型：对每一个人提取特征的model是经过调优了的，其他部分与组分类一致
- 图像特征时序模型：第一个baseline的时序拓展
- 组特征时序模型：第二个baseline的时序拓展
- 不带第一个LSTM层的双阶段模型（忽略person-level时序模型）
- 不带第二个LSTM层的双阶段模型（忽略group-level时序模型）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206281348539.png" alt="image-20220628134845501" style="zoom:50%;" />

**state-of-arts对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206281502224.png" alt="image-20220628150236180" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206281504089.png" alt="image-20220628150402040" style="zoom:50%;" />

论文提出了一个新的**volleyball数据集**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206281529210.png" alt="image-20220628152902169" style="zoom:50%;" />

文章也说了，这个数据集是一个很有挑战性的数据集，因为能够决定组行为类别的个人行为类别，在数据集中较少出现。作者在这个数据集上进行实验：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206281521593.png" alt="image-20220628152158560" style="zoom:50%;" />
