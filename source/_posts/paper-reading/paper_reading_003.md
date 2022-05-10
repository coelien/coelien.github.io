---
title: 论文阅读笔记：”Large-scale Video Classification with Convolutional Neural Networks“
tags: 
- 行为分类
- 时间信息融合
categories:
- 论文阅读
---

# Large-scale Video Classification with Convolutional Neural Networks

> 这一篇论文因其经典性，着重探索的各种模型的连接性，讨论如何更好地去获得时间信息，可以说是之后的C3D，Res3D，R(2+1)D的思想的先驱，有较大的参考价值，故阅读并写下笔记。

## Introduction

这篇文章的研究背景是利用CNN在大规模视频数据集上进行分类。此时的网络将不只能够接触到静态表观信息，还能够接触到复杂的时序变化信息。这篇文章研究了扩展CNN在时间域上的连接性的诸多方法，这些方法有效利用了局部时空信息。文章还提出了一种能够加速训练的多分辨率方法。

论文首先从实践的角度，判断当时没有合适的视频分类基准数据集。为了获取足够充分的数据来训练CNN，作者团队自己采集了名为Sports-1M的数据集。

从建模的角度，作者以问题为导向，分析了在CNN中，什么样的时间连接模式在利用视频中的局部动作信息是最佳的；而这些额外的运动信息是如何影响模型的预测的；它对性能的提升究竟有多大。作者尝试了在时间域中结合信息的不同方法。

从计算的角度，因为训练CNN需要优化数百万参数，非常消耗时间，提高计算能力，对于网络结构的搜索和迭代较为重要。如上图所示，作者提出了双流法，将处理过程分为了两个结构相同的场景流和中心流。场景流学习低分辨率帧的特征，而中心流只学习图像中心的特征。使用这种方法大大提高了计算速度，同时对网络的性能几乎没有影响。

## Models

### CNN 时间信息融合

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202112231703437.png" alt="image-20211223170350392" style="zoom:50%;" />

如上图所示，最左边的single frame模型是baseline模型，而右边的三种不同时间融合方式的模型是它的扩展。最左边的single frame baseline模型只提取了视频中的静态表观信息；第二个late fusion模型设置了两个平行的且共享参数的single-frame网络，这两个相隔15帧的流在第一个全连接层中进行汇聚；early fusion在像素级别上就将整个时间窗口上的多帧信息进行融合，这时其第一个卷积层的过滤器的shape就变为了$11*11*3*T$​​，实际上所做的就是3维卷积；slow fusion结合了前两种方法，在整个网络中缓慢地融合时间信息，这样高层能同时获取较为同步的全局时空信息，整个表观特征的提取和时序信息的提取是同时进行的。3D卷积可以参考论文 （Sequential deep learning for human action recognition，2011）和（3D convolutional neural networks for human action recognition， PAMI 2013）

### 多分辨率CNN

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202112231704560.png" alt="image-20211223170415501" style="zoom:50%;" />

如上图所示，为了试验不同的网络结构和不同的超参，作者提出了双流法，将处理过程分为了两个结构相同的场景流和中心流。这种双流方法改变了网络结构，但缩减了运行时间的同时又没有牺牲性能。场景流学习低分辨率帧的特征，而中心流只学习图像中心的特征。使用这种方法将帧分辨率从$178*178$降低为$89*89$，大大提高了计算速度。

### 学习过程

- 优化

  它使用的优化算法为 Downpour Stochastic Gradient Descent，可以参考查看这篇文章（Large scale distributed deep networks，NIPS 2012）

- 数据增强和预处理

  首先，裁剪所有图片到中央区域，放缩图片到$200*200$，并在其中随机采样一个大小为$170*170$的区域，最终以50%的概率对图片进行水平随机翻转，并进行中心化

## Results

（待完成）
