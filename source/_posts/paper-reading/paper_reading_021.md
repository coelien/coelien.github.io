---
title: 论文阅读笔记：“TSM：Temporal Shift Module for Effificient Video Understanding”
tags: 
- 2D model
- TSM
categories:
- 论文阅读
- 行为识别
---

# TSM: Temporal Shift Module for Effificient Video Understanding

> 对于实际部署来讲，硬件高效的视频理解方法十分重要。论文提出时也有一些方法，来tradeoff建模与计算量，但是论文提出，已知的方案牺牲了低级的时序建模以换取效率，所以一些重要特征的损失在所难免。

## 核心亮点

**TSM模块**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206191644911.png" alt="image-20220619164448871" style="zoom:50%;" />

TSM模块的额外计算在概念上是没有的，只有涉及数据移动的实际延迟，但该模块有很强大的时空建模能力

- 时序局部平移策略：只平移一部分channels
- 在何处使用TSM模块？在残差分支使用，不会对2D CNN的空间特征学习能力造成损害。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206191643344.png" alt="image-20220619164355314" style="zoom:50%;" />

**为什么要平移？(卷积的替代)**

**一维上**讲，1D卷积可以分解为平移和乘加操作，形式化地可以表示为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206191726650.png" alt="image-20220619172654625" style="zoom:50%;" />

因此，乘加操作就可以表示为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206191728194.png" alt="image-20220619172810169" style="zoom:50%;" />

**二维上沿空间维度**平移，我对一维的思想做了延申：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206191732532.png" alt="image-20220619173217418" style="zoom: 33%;" />

对于a12这个位置而言，平移和乘加操作的计算公式为：$a_{22}*w_{11}+a_{13}*w_{21}+a_{02}*w_{22}+a_{11}*w_{12}+a_{12}*{x}$，但是这个结果成十字形，与原先2D卷积相差较大。

**三维上沿时间维度**进行平移，如下图所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206191740916.png" alt="image-20220619174041893" style="zoom: 67%;" />

对于每一个插入TSM的模块，其时间感受野都增加了2，因此对其进行2D卷积时，就好像同时在时间维度进行filter size为3的卷积一样。

**TSM的好处不言而喻**

- 使用TSM模块可以把任意一个已有的2D网络模块变成伪3D模块，它能够同时持有空间和时间信息

- 部署来讲是硬件高效且友好的，低延迟推理，低内存消耗，只需支持2D卷积操作即可

- 支持在线推理，对于实时性要求高的应用很重要：

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206191755584.png" alt="image-20220619175511542" style="zoom:50%;" />

  对于前一帧的特征图，我们截取它的1/8并保存到内存中，在当前帧中用缓存的1/8的特征图替换当前帧特征图的对应部分。从图中我们可以发现，TSM是一种多层次的特征融合模块。
  
## 实验结果

**基线对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206201223833.png" alt="image-20220620122329792" style="zoom:50%;" />

从图中可以看出，对于时序关系相对重要的数据集，加入TSM模块之后的精度提升可达２位数的提升。

**泛用性证明**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206201226539.png" alt="image-20220620122629511" style="zoom:50%;" />

对于不同的2D骨干网络，TSM模块的加入都能提升精度，即使对于NL R-50这种已具备时序建模能力的网络来说。

**state-of-arts证明**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206201246721.png" alt="image-20220620124647656" style="zoom:50%;" />

第一个区使用了late temporal fusion;第二个区域允许中级时序融合；第三个区域允许全层次的时序融合。

**精度-性能对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206201252425.png" alt="image-20220620125227388" style="zoom:50%;" />

图中圆的面积代表参数量的大小，从图中可以看出TSM模型是精度高且效率高的。

**性能对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206201257047.png" alt="image-20220620125706010" style="zoom:50%;" />

**在线推理**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206201305614.png" alt="image-20220620130507573" style="zoom:50%;" />

由图可知，TSM模块对于低延迟的实时推理十分有用。图二是仅观察一部分的帧后的预测精度，可以看出TSM模型提早预测的能力较强。
