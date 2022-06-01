---
title: 论文阅读笔记：“MobileNetV2：Inverted Residuals and Linear Bottlenecks”
tags: 
- MobileNet
- Inverted Residuals
- architecture design
- Linear Bottlenecks
categories:
- 论文阅读
- 图像分类
---

# 论文阅读笔记：MobileNets系列论文浅析：mobilenet v2

> 参考[原论文](https://arxiv.org/abs/1801.04381v4)
>
> 参考[B站视频](https://www.bilibili.com/video/BV1yE411p7L7/)
>
> hint: 为了去寻找一个最优的网络架构，有哪些思考模式是可以借鉴的。哪怕我无法提出一个通用的网路架构design的创新方法，学习已有的架构设计思路来改进已有模型也是可以的，所以为了改进模型而去学习这些改进的思路是很必要的。

> 究竟有哪些架构优化方法呢？
>
> - 强化学习: [[1]](#1)
> - 通用算法：[[2]](#2)

## 核心亮点

**manifold of interest**

- manifold: 流形的概念:

> 流形学习的观点是认为，我们所能观察到的数据实际上是由一个低维流形映射到高维空间上的。由于数据内部特征的限制，一些高维中的数据会产生维度上的冗余，实际上只需要比较低的维度就能唯一地表示。参考链接：https://en.wikipedia.org/wiki/Manifold

- 举例1：实际上二维空间中的圆就是一个一维流形（维度冗余）
- 举例2：三维空间中一个球面（只需要经纬两个维度即可描述）

在深度学习领域，我们可以将激活张量视为多个容器组合而成的，每个容器包含了数量为$h_i*w_i$的pixels向量，每个向量的维度为$d_i$。这些激活张量的集合组成了流形特征（interest）。而且认为神经网络中的流形特征可以被嵌入到低维子空间中。正是这个概念，令如mobilenetv1（Width Multiplier），shufflenet等网络取得了不错的效果。



<span id="1">[1] Neural architecture search with reinforcement learning. CoRR 2016</span>

<span id="2">[2]  Learning transferable architectures for scalable image recognition. CoRR 2017</span>

