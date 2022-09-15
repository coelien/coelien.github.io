---
title: 论文阅读笔记：“Squeeze-and-Excitation Networks”
tags: 
- SE块
- architecture design
categories:
- 论文阅读
- 图像分类
---

# Squeeze-and-Excitation Networks(2019)

## 引言分析

之前的行为识别研究主要是针对时空依赖，时空注意力进行建模（模块设计），去改进现有的一个网络架构。这篇文章从通道的角度研究了一种不同的网络设计方法。

## 核心亮点

**SE-Block**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181358077.png" alt="image-20220718135843032" style="zoom:50%;" />

SE块可以显示地对通道之间的相互依赖进行建模，以得到一个更好的特征表达。这个模块做的实际上就是特征重标定，通过利用全局信息去选择性的增强有用信息并抑制无用信息。如图所示，该模块分为了两个操作：squeeze(挤压)和excitation（激活）。squeeze操作实际上就是沿着空间维度聚合特征图，得到一个沿着通道维度全局分布的特征响应。excitation是一个门控机制，以embeddings为输入，产生一个每个通道的权重集合。

**Sqeeze：空间信息聚合**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207191027476.png" alt="image-20220719102714390" style="zoom:50%;" />

这里的squeeze操作作者采用的是全局平均池化，来生成每个通道的统计数据。

**Excitation：自适应重标定**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207191031603.png" alt="image-20220719103155575" style="zoom:50%;" />

论文采用了一种简单的门控机制来进行重标定，因为s是通道权重的集合，SE块也是固有地引入了基于输入的动态性，所以也可以看作基于全局通道的自注意力机制。

**将SE模块融入现有的模块中**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207191038648.png" alt="image-20220719103828587" style="zoom:50%;" />

## 网络结构

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211055132.png" alt="image-20220721105513082" style="zoom:50%;" />

如图在每个残差块的尾端加入SE块

## 实验结果

**加入SE模块的效果与网络深度的关系**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211049996.png" alt="image-20220721104924915" style="zoom:50%;" />

如表所示，resnet-152的 top-5 error高于SENet-101的top-5 error，证明了SENet的精度增长不仅仅是由深度带来的，而且深度越深，精度越高，证明使用SE块和增加深度是相互补充的。

**与现代架构进行集成的效果**

从上表和下表可以看出，将SE块融入主流backbone可以显著提高精度。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211058802.png" alt="image-20220721105843772" style="zoom:50%;" />

**SE块的泛用性不限于网络架构也不限于数据集**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211100366.png" alt="image-20220721110020327" style="zoom:50%;" />

可以看出，对于其他数据集，分类精度也有显著提高。

**SE的泛用性也不局限于图像分类任务**

作者在场景分类、目标检测任务上进行了实验，结果均有提升：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211102018.png" alt="image-20220721110253983" style="zoom:50%;" />

**与state-of-arts的对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211105469.png" alt="image-20220721110510427" style="zoom:50%;" />

**消融实验1：缩减比率对于SE-ResNet50的影响**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211105265.png" alt="image-20220721110547234" style="zoom:50%;" />

**消融实验2：squeeze操作符对精度的影响**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211108297.png" alt="image-20220721110807268" style="zoom:50%;" />

**消融实验3：excitation操作符对精度的影响**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211108441.png" alt="image-20220721110828414" style="zoom:50%;" />

**消融实验4：SE块的集成阶段对精度的影响**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211110973.png" alt="image-20220721111019941" style="zoom:50%;" />

**消融实验5：SE块的集成的具体位置对精度的影响**

![image-20220721111119437](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211111486.png)

如上图，作者设计了不同SE模块插入位置的集成块，实验结果如下表：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207211110812.png" alt="image-20220721111038771" style="zoom:50%;" />

值得注意的是，mobilenetV3的创新点之一和table15所做的实验是重合的，但是这里只是简单说将SE模块可以放入残差块里，mobilenetV3做了充分的实验对其进行验证和补充。
