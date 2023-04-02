---
title: 论文阅读笔记：“Spatio-Temporal Dynamic Inference Network for Group Activity Recognition”
tags: 
- Group Activity Recognition
- DIN
- Dynamic relation
- relation reasoning
categories:
- 论文阅读
- 群体行为识别
---

# Spatio-Temporal Dynamic Inference Network for Group Activity Recognition(iccv 2021)

## 引言分析

GAR激起了研究兴趣因其广泛的应用：安保/运动视频分析，社会场景理解等。目前提出的主要模块都包含了时空交互因素以得到行为特征。如何对主体的相互作用进行建模也得到了广泛的研究。主要包括RNN，注意力机制和GNN。目前GNN已成为GAR中经常采用的算法，它在构建的语义图上来传递信息。但是之前使用GNN的方法对个体之间的交互均在预定义的图上进行建模。这种方法可行，但有着很多缺陷：

- 与给定主体交互的那些客体应该是**主体特有的**，或者说**针对主体的**，而不应该是**预定义的**。而一个预定义的图并不适合所有人的关系推理。
- 之前预定义的图模型在一个全连接或是十字交叉的图上来对交互关系进行推理。它很容易导致**过平滑**(over-smoothing)，令特征难以区分。而且在长视频剪辑或是场景中有很多人的情况下，会导致极高的计算复杂度。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207281434926.png" alt="image-20220728143440754" style="zoom:50%;" />

如上图，a、b、c分别代表全连接图推理、十字交叉图推理和所提出的针对人的自适应的动态图推理。这种动态图推理对于每一个要更新的绿色结点来说，都是独特而唯一的。

## 网络结构图

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207281515136.png" alt="image-20220728151522007" style="zoom:50%;" />

上图是作者提出的框架：动态推理网络(DIN)。从逻辑上可以分为时空特征提取和推理模块两部分，第一部分得到的是个体特征的集合$X\in\R^{T\times N\times D}$。论文提出的DR和DW模块动态地对每个特征预测一个特定的交互关系图。根据这些图我们可以对特定特征进行更新。

## 核心亮点

![image-20220728153718703](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207281537853.png)

**动态关系 Dynamic Relation**

这里的动态指的是，关系矩阵仅仅依赖于初始化的交互场中的特征。所依赖的特征不是固定的，而是动态变化的。

<img src="C:/Users/sixwa/AppData/Roaming/Typora/typora-user-images/image-20220728161252942.png" alt="image-20220728161252942" style="zoom:50%;" />

上面是计算第i个特征关系矩阵的表达式。这个算法和ARG的差异很大。这个应该是可变形卷积，之后再读相关论文进行理解。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207281616810.png" alt="image-20220728161612785" style="zoom:50%;" />

上面的特征更新表达式从形式上与ARG的图卷积层是基本一致的，区别主要在于范围一个是K，另一个是T*N。

**动态漫游 Dynamic Walk**

论文的目的是通过DW模块来对复杂的时空依赖进行建模，它只使用了大小局限的交互场。首先，对于给定的第i个个体特征，我们要预测交互场范围内的时空动态漫步偏移量$\Delta P_i=\{\Delta p_{i,k}|k=1,...,K\}=W_pu_i+b_p$，其中$W_p \in \R^{(K\times 2)\times(K\times D)}$ 为线性投影矩阵，$u_i$为所有交互场内堆叠起来的特征向量。得到漫步偏移量后，动态漫步的特征的计算公式如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207281640994.png" alt="image-20220728164025967" style="zoom:50%;" />

其中，$\Delta p_{i,k}$为第i个交互场的第k个特征的坐标。

**结合DR和DW**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207281641934.png" alt="image-20220728164121912" style="zoom:50%;" />

同过上式我们可以结合DR和DW来进行动态更新。

## 实验结果

**消融实验**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202208011100187.png" alt="image-20220801110017091" style="zoom:50%;" />

论文进行消融实验来说明提出方法的有效性。MCA和MPCA分别代表，分类准确率和平均类准确率。

## 代码阅读

整体的训练代码包含了两个阶段。第一阶段微调一个backbone，使之可以适应Volleyball数据集；第二阶段是训练+动态推理。

第一部分利用action loss和activity loss来学习模型。模型的架构图如下所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210121041793.png" alt="image-20221012104148681" style="zoom:50%;" />

参数数量，及所占内存如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210121042777.png" alt="image-20221012104233749" style="zoom:50%;" />

第二阶段动态推理的模型框架图如下所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210121130956.png" alt="image-20221012113006895" style="zoom:50%;" />

