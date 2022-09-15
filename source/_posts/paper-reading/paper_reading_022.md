---
title: 论文阅读笔记：“Learning Spatiotemporal Features with 3D Convolutional Networks”
tags: 
- 3D model
- C3D
categories:
- 论文阅读
- 行为识别
---

# Learning Spatiotemporal Features with 3D Convolutional Networks

> 这篇文章提出了C3D模型，成为了之后研究的基础性原型，值得注意的是作者使用了一系列可视化方法对C3D学习到的特征进行可视化，对于今后的模型的研究可能会有所裨益。

## 核心思想

**特征的紧凑性：compact**

使用PCA将特征降低到低维度，通过线性SVM得到低维度特征在数据集上的分类精度：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206231637409.png" alt="image-20220623163658335" style="zoom:50%;" />

**特征的泛用性：generic**

通过在其他数据集上进行可视化，使用了t-SNE方法，将得到的fc6特征投影到2D空间里：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206231648802.png" alt="image-20220623164829755" style="zoom:50%;" />

从图中可以看出，特征嵌入在其他数据集上的表现良好 

## 模型结构

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206231651204.png" alt="image-20220623165156169" style="zoom:50%;" />