---
title: 论文阅读笔记：“HiGCIN： Hierarchical Graph-based Cross Inference Network for Group Activity Recognition”
tags: 
- Group Activity Recognition
- CIB
- 时空共时依赖
categories:
- 论文阅读
- 群体行为识别
---

# HiGCIN: Hierarchical Graph-based Cross Inference Network for Group Activity Recognition(TPAMI 2020)

## 引言分析

在组行为识别中，为了对组行为进行推理，我们需要获取包括身体区域，人，以及它们之间的联系的各种信息。个体行为与群里行为是强耦合的，而之前的一些方法使用了两阶段的pipeline，将个体行为与群里行为的推导相割裂，或者说传统方法忽略了人与人之前的潜在的时空依赖，只考虑了空间情景。同时，这些方法也没有考虑身体区域之间的时空依赖。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151259510.png" alt="image-20220915125950416" style="zoom:50%;" />

因而论文提出了一个泛用的十字推理块（CIB）来利用隐藏在特征结点之间的时空依赖。基于CIB，作者提出了群体行为识别框架HIGCIN。设计了 推理身体区域之间依赖的BIM和人之间依赖的PIM。

## 核心亮点

**网络整体架构**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151348015.png" alt="image-20220915134830849" style="zoom:50%;" />

如图所示，BIM模块考虑了固有的身体区域之间的时空联系，提取每个人的卷积特征。在这之后，使用PIM模块进一步探索人之间特征的时空依赖。最终每个帧的所有特征都被max-pooled到了单个向量。

**十字推理**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151356092.png" alt="image-20220915135600042" style="zoom:50%;" />

受到non-local网络的启发，时空共时依赖可以通过非局部推理得到，本文提出了一种新的推理方式十字推理，去掉了很多不必要的连接。

**CIB**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151400180.png" alt="image-20220915140011135" style="zoom:50%;" />

在时间t，第s个特征节点$x^s_t$(图中的橘色向量)的空间表示为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151403463.png" alt="image-20220915140352442" style="zoom:50%;" />

s'为所有在空间域可能与其有交互的其他结点。r用来计算任意两个特征的空间依赖，g是线性变换。

同理，时间表示为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151408349.png" alt="image-20220915140837325" style="zoom:50%;" />

二元函数r一般采用点积相似度：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151410430.png" alt="image-20220915141002409" style="zoom:50%;" />

通过结合空间表示和时间表示，我们可以得到：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151411320.png" alt="image-20220915141155295" style="zoom:50%;" />

它包含了时空共时依赖信息。之后作者用了残差连接来将设计的block包含进现有的深度架构中：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151414245.png" alt="image-20220915141448224" style="zoom:50%;" />

该CIB模块可以被拓展至探索身体区域之间与人之间的联系。

**BIM**

作者认为人的结构信息个体行为的特征表示有一定帮助。首先使用CNN得到大小为$H\times W$的D个特征图。对于每个特征图的每个元素，我们将其看作一个身体区域。对于第k个人，其身体区域特征为$F^k \in \R^{T\times P\times D}$。考虑身体区域之间的联系后，可得：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151425367.png" alt="image-20220915142515341" style="zoom:50%;" />

通过平均池化，我们即可得到第k个人的特征$B^k \in \R^{T\times D}$。

整个过程如下图所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151428015.png" alt="image-20220915142801946" style="zoom:33%;" />

**PIM**

同理，通过上一步得到B之后，可通过如下的公式计算人之间的联系：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151430357.png" alt="image-20220915143002333" style="zoom:50%;" />

过程如下图所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151431666.png" alt="image-20220915143100582" style="zoom: 33%;" />

最终的loss函数定义如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151433551.png" alt="image-20220915143323529" style="zoom:50%;" />

NY代表群体行为的类别数，lt是预测，带帽子的是真值。

## 实验结果

**指标**

- MCA：正确预测的百分比
- MPCA：每个类别的平均准确率

- 混淆矩阵

**实验组对比**

- b1：基线
- b2：使用BIM
- b3：使用PIM
- b4：同时使用BIM和PIM

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151444981.png" alt="image-20220915144443947" style="zoom:50%;" />

**SOTA对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151445912.png" alt="image-20220915144556869" style="zoom:50%;" />

**是否加入个体标签对实验的影响**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151449953.png" alt="image-20220915144954914" style="zoom:50%;" />

**混淆矩阵**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151450492.png" alt="image-20220915145044427" style="zoom:50%;" />

**可视化**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151452924.png" alt="image-20220915145248655" style="zoom:50%;" />

上面矩阵是根据r函数计算得到的。

![image-20220915145909624](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209151459869.png)
