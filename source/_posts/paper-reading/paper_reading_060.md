---
title: 论文阅读笔记：“TokenLearner：What Can 8 Learned Tokens Do for Images and Videos?（NIPS 2021)”
date: 2023-03-06
tags: 
- Activity Recognition
categories:
- 论文阅读
- 行为识别
---

# TokenLearner: What Can 8 Learned Tokens Do for Images and Videos?（NIPS 2021）

## 网络结构

**Framework for Video**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303061003937.png" alt="image-20230306100347875" style="zoom:50%;" />

TokenLearner自适应地学习标记向量的集合，MHSA对时空关联进行建模，最后TokenFuser将它们结合起来，并重建为原始的输入tensor大小。

**TokenLearner**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303060939702.png" alt="image-20230306093942601" style="zoom:50%;" />

**TokenFuser**

- token-wise linear layer: 在各个token间融合时空信息

  $\R^{ST}\Rightarrow\R^{ST}$

- token特征的反向映射(映射回原先的tensor shape)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303060945389.png" alt="image-20230306094504359" style="zoom:50%;" />

**Embedding into ViT architecture**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303060941608.png" alt="image-20230306094153553" style="zoom:50%;" />

## 实验

**kinetics400上的SOTA对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303061014204.png" alt="image-20230306101409167" style="zoom:50%;" />

**charades数据集上的模型优化**

将TokenLearner嵌入X3D中，将3D卷积换成了一对2D卷积和1D卷积。1D卷积采用是TokenLearner进行替换。并把MHSA替换未来vector transformer。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303061037865.png" alt="image-20230306103703827" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303061045394.png" alt="image-20230306104545348" style="zoom:50%;" />