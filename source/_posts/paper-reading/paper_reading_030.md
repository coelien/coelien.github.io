---
title: 论文阅读笔记：“Non-local Neural Networks”
tags: 
- NL块 
categories:
- 论文阅读
- 行为识别
---
# Non-local Neural Networks

## 引言分析

这篇论文提出了**非局部化操作**(non-local)作为可以捕获长期依赖的基础搭建块。文章首先谈到了诸如卷积和循环神经网络的方法都只能对其近邻进行处理，有着计算复杂、难以调优、不能进行多点依赖建模(multi-hop dependency)，之后谈到了使用局部化操作的好处：能捕获长期依赖、高效、不会改变输入大小、即插即用。然后，作者将局部化操作应用于视频分类的任务中，一个单独的non-local块可以直接捕获时空依赖。为了说明非局部操作的泛用性，作者在目标检测和分割，姿态估计等任务上做了实验，都取得了不错的效果。

## 核心亮点

**non-local 块**

理解non-local块的前提是理解非局部操作：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207180921694.png" alt="image-20220718092053605" style="zoom:50%;" />

简单来讲，它计算的是第i个位置与其他所有位置的关系，进行求和与归一化后的值。在我的理解看来，$f(x_i,x_j)$就像是两个位置的关系进行建模，而$g(x_j)$是这个关系的权重。

论文简单的只考虑了g作为线性嵌入的情况$g(x_j)=W_jx_j$。**二元函数f的选择**是论文讨论的重点：

- 高斯: $f(x_i,x_j)=e^{x_i^Tx_j}$
- 嵌入高斯: $f(x_i,x_j) = e^{\theta(x_i)^T\phi(x_j)}$
- 点积: $f(x_i,x_j) = \theta(x_i)^T\phi(x_j)$
- 连接: $f(x_i,x_j) = ReLU(w^T_f[\theta(x_i),\phi(x_j)])$

文章还说明了**自注意力模块**与嵌入高斯版的非线性操作的异同，他们的最终形式是一样的：$y=softmax(x^TW^T_{\theta}W_{\phi}x)g(x)$。但是作者拓展得到时空非局部网络能够处理多样的输入，具有泛用性。下面简单推导了一下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207180945051.png" alt="image-20220718094508970" style="zoom:50%;" />

值得注意的是**y**是yi的向量，而每一个yi又是划线式子的点积，因此若要转换成向量化操作也很容易(p.s. softmax函数里的式子是没有底数e的，手误)(我记得cs231n有讲softmax向量化操作)。

之后搭建non-local块有意思的点在于如何将非局部化操作融入进去，作者将non-local块形式化地定义为：$z_i=W_zy_i+x_i$。实际上即使用了残差连接，它有诸多好处：

- 能够让我们将新的non-local块插入任何预训练模型但不改变他的任何行为，只需将$W_z$初始化为0即可。
- 而是可以增强特征（+）, 同时防止梯度消失。

具体的non-local块结构图如下所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181000672.png" alt="image-20220718100033581" style="zoom:50%;" />

难以理解的点在上文均以阐述，这里需要注意的是论文使用$1*1*1$的卷积来进行嵌入(embedding)，他的另一个作用是可以将计算量减少一半。还可以使用子采样的技巧进一步降低复杂度：在$\phi,g$后加一个max_pooling层，可以令计算更加稀疏。

## 网络结构

**2D baseline**

之所以以2D为baseline，是为了隔离时间建模因素对模型的影响，网络结构如下图所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181221245.png" alt="image-20220718122144201" style="zoom:50%;" />

这个baseline只是简单地聚合了时间信息

**I3D**

我们每两个残差块inflate一次卷积核：从$k*k$->$t*k*k$

**Non-local network**

## 实验结果

**non-local块的f函数的选择**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181242190.png" alt="image-20220718124201161" style="zoom:50%;" />

由实验可以看出f函数的选择对精度的影响不大，为了方便作者选择了嵌入高斯。

**加入non-local块的位置选择**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181246126.png" alt="image-20220718124617098" style="zoom:50%;" />

如图，加入到3，4 stage都是合适的

**加入non-local块数的影响**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181250629.png" alt="image-20220718125004600" style="zoom:50%;" />

而且该实验也证明了精度的提升并不全部来自网络深度的增加

**时间-空间-时空维度的non-local操作对模型的影响**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181256182.png" alt="image-20220718125601152" style="zoom:50%;" />

**non-local与3D模型的对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181258230.png" alt="image-20220718125842199" style="zoom:50%;" />

上表展示了两种对时间建模的方式比较，可见 C2D+5 non-local块对于网络的精度提升最高，同时其计算量更少。

**non-local与3D conv的互补性**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181329241.png" alt="image-20220718132901210" style="zoom:50%;" />

**更长的输入对网络的影响**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181332706.png" alt="image-20220718133234678" style="zoom:50%;" />

**与state-of-arts的对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181334875.png" alt="image-20220718133444825" style="zoom:50%;" />

从中可见，NL-I3D模型没有使用flow和audio信息，但是它的精度却超过了那些使用了的方法。
