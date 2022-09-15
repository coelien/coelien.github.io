---
title: 论文阅读笔记：“Temporal Pyramid Network for Action Recognition”
tags: 
- TPN
- 信息流聚合
- 时空调制
categories:
- 论文阅读
- 行为识别
---

# Temporal Pyramid Network for Action Recognition(2020)

## 引言分析

文章一开始就点明了时间节奏对识别行为的重要性，对于类间与类内的在时间节奏上的变化进行精确建模，可以对行为识别带来巨大的性能提升。之前尝试提取动作实例的动态视觉节奏的方法主要依赖于帧金字塔，即在金字塔的不同级别上用不同的采样帧率采样输入帧。但是这些方法的缺陷也很明显，即每个帧率的通路都需要一个单独的backbone来进行处理，从而计算复杂度极高。作者观察到，当网络的层次越深时，他们的时间感受野也会变大。因此，在同一个模型中不同深度的特征已经具有捕获快节奏和慢节奏运动的能力。作者由此提出了一个时间金字塔网络来在特征级别上聚合多样的视觉节奏信息。此外TPN还具有即插即用的能力

## 核心亮点

**TPN框架**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209081419670.png" alt="image-20220908141927598" style="zoom:50%;" />

TPN框架包含了两个重要部分：特征源以及特征聚合。作者使用空间语义调制和时间节奏调制来控制特征源的相对差异。使用多种信息流来进行特征聚合。

**TPN特征源**

对于M个层次化的特征，它们从底向上有着不断增加的时间感受野。可以用两种方法来从backbone网络中收集这些特征：

- 单深度金字塔
- 多深度金字塔

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209081430578.png" alt="image-20220908143032549" style="zoom:50%;" />

**空间语义调制**

调整各层特征的空间形状及感受野，使他们相互匹配。一个辅助的分类头也被加入了网络以获得更强的监督，总体的目标函数如下：

![image-20220908143652982](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209081436005.png)

**时间速率调制**

在空间调制后，我们需要对第i层的特征进行因子为ai的降采样，以控制不同时间尺度特征的相对差异

**信息流聚合**

对于第i层的特征，我们可以采用以下三种基本操作来进行聚合：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209081448773.png" alt="image-20220908144812741" style="zoom:50%;" />

将B-U流和T-D流结合，我们可以得到两种额外的信息流：cascade流和parallel流。信息流聚合流程如下图：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209081451275.png" alt="image-20220908145132230" style="zoom:50%;" />

## 实验结果

**SOTA on Kinetics-400**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091454106.png" alt="image-20220909145458064" style="zoom:50%;" />

**泛用性证明**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091454270.png" alt="image-20220909145431240" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091457782.png" alt="image-20220909145707751" style="zoom:50%;" />

**如何选择待处理的特征源？**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091508956.png" alt="image-20220909150830923" style="zoom:50%;" />

当我们选择相对浅层的特征源的时候，精度会略有下降。

**信息流聚合方法的重要性**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091513129.png" alt="image-20220909151320098" style="zoom:50%;" />

实验证明，top-bottom和bottom-top这两种信息流是相互补充的

**空间语义调制和时间速率调制的重要性**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091532332.png" alt="image-20220909153254301" style="zoom:50%;" />

**输入帧数量的影响**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209091535101.png" alt="image-20220909153526059" style="zoom:50%;" />
