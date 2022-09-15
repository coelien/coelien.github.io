---
title: 论文阅读笔记：“Learning Spatio-Temporal Representation with Pseudo-3D Residual Networks”
tags: 
- 3D model
- p3d
categories:
- 论文阅读
- 行为识别
---

# Learning Spatio-Temporal Representation with Pseudo-3D Residual Networks

> 这篇文章其实挺简单的，思路和很多的顶会略有重合，注意比较他们之间的差异

## 核心亮点

**P3D 块**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111357498.png" alt="image-20220711135720380" style="zoom:50%;" />

论文提出将3D卷积解耦为编码空间信息的2D空间卷积和编码时间信息的1D时间卷积。上图是作者设计的三种不同的P3D块，之后基于这三种P3D块，作者加入了bottleneck设计：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111402977.png" alt="image-20220711140206924" style="zoom:50%;" />

这种设计可以有效减少计算复杂度。

**结构多样性**

论文提出的P3D Resnet混合了上面的三种P3D块：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111405541.png" alt="image-20220711140537517" style="zoom:50%;" />

这种设计思路是由在深度网络种追求结构多样性的成果实践启发而来的。如图我们在P3D网络中对上面的三种结构循环使用，对比实验如下表所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111413148.png" alt="image-20220711141341114" style="zoom:50%;" />

## 实验结果

**在Sports-1M上进行对比实验**

![image-20220711142018504](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111420543.png)

**在三种不同的任务上验证P3D学习的视频特征**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111423319.png" alt="image-20220711142333261" style="zoom:50%;" />

未来可以加入光流输入来增强P3D的分类能力。

**学习到的特征的稳定性**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111426788.png" alt="image-20220711142604755" style="zoom:50%;" />

**学习到的特征的区分能力**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111427623.png" alt="image-20220711142752586" style="zoom:50%;" />