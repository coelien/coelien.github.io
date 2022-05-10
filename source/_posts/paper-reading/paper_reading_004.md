---
title: 论文阅读笔记：”Social GAN: Socially Acceptable Trajectories with Generative Adversarial Networks“
tags: 
- 轨迹预测
- RNN
- GAN
categories:
- 论文阅读
---
# 	Social GAN阅读笔记（cvpr 2018）

## 方法

### 问题定义

论文的目标在于同时推理和预测在一个场景里的所有实体的未来行动轨迹。假设我们可以获得场景里人们的所有轨迹信息为$X=X_1,X_2,...,X_n$，要同时预测所有人的未来轨迹信息为$\hat{Y}=\hat{Y_1},\hat{Y_2},...,\hat{Y_n}$。行人i的输入轨迹为$X_i=(x^t_i,y^t_i)$，t=1,...,$t_{obs}$，真实未来轨迹为$Y_i=(x_i^t，y_i^t),t=t_{obs}+1,...,t_{pred}$

### GAN

论文模型包含三个关键组件：生成器G，池化模块PM，和判别器D。G是基于编解码框架的网络，论文使用了PM模块来连接编码器和解码器的隐状态。模块G提取输入Xi并输出预测轨迹$\hat{Y}_i$。模块D输入整个包含$X_i$和$\hat{Y}_i$的序列，并把他们分类为真或假。SGAN的整体结构如下图所示：

![image-20220102184313441](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201021843497.png)

#### GENERATOR

在模块G中，论文首先使用一个单层的多层感知机把每个行人的位置信息嵌入并得到了固定长度的向量$e^t_i$，这些嵌入量之后用来作为编码器中的LSTM单元t时刻的输入。并遵从如下的迭代关系式：

![image-20220102185147460](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201021851490.png)

其中，$\phi()$是一个带有ReLu非线性的嵌入函数，Wee是嵌入权重，LSTM单元的权重$W_{encoder}$被场景里的所有人所共享。编码器可以学习行人的状态并存储他们的历史运动。在论文的方法中，作者是通过PM模块来对人与人之间的交互建模。在$t_{obs}$时刻后，我们对所有场景里的人的隐状态信息进行池化。论文对decoder的隐变量状态进行了初始化：

![image-20220102190609514](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201021906545.png)

其中，$\gamma()$是一个带有ReLu非线性的多层感知机，Wc是嵌入权重。在初始化解码器状态之后，我们可以根据以下步骤来获得预测轨迹：

![image-20220102191015871](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201021910900.png)

### Pooling Module

![image-20220103155639067](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201031556153.png)

对于行人1来说，其位置为(x1,y1)，将场景内其他人相对行人1的位置坐标（如$(x_2-x_1,y_2-y_1)$）和隐状态传给多层感知机，之后使用最大池化来作为对称函数，池化的结果向量P1如右图所示。

### 多模态输出

论文提出了一种损失函数，它可以促进网络生成多样的样本。对于每个场景，我们生成了K个可能的预测输出，并选择其中最好的预测作为最终的预测。

![image-20220103162323082](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201031623105.png)

### 实现细节

作者在GAN中使用了长短时记忆这一特殊的RNN来处理序列位置数据。编码器的隐状态维度为16，而解码器的隐状态维度为32。我们将输入坐标序列编码为了16维向量。作者使用Adam优化算法训练生成器和判别器200个epoch。其batch size大小为64，初始学习率为0.001。

## 实验

### 指标

1. 平均位移误差（ADE）：在所有预测时间点的L2距离
2. 最终位移误差（FDE）：在预测最终位置和真实最终位置间的距离

### 基线

1. 线性：一个最小化最小二乘误差的线性回归器
2. LSTM：一个没有pooling机制的简单LSTM
3. S-LSTM：每个行人通过一个带隐状态的LSTM进行建模，该隐状态在LSTM的每个时间点使用social pooling layer进行池化

### 消融实验

论文对于不同的控制设定进行了消融实验。在测试时，论文从模型中采样N次并选择最好（L2距离小）的预测用来进行定量评估。

### 评估方法

论文使用了留一法来评估模型，即将数据集分为5个集合，每次在其中的4个数据集合上进行训练，在剩余的一个数据集合上进行测试，重复训练5次。论文观测8个时间点（总计3.2秒）的历史轨迹，并预测8和12个时间点的未来轨迹。

### 定量分析

- 指标精度

![image-20220103170421282](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201031704349.png)

因为线性模型只能够建模直线路径，所有其表现没有LSTM和S-LSTM好，因为他们可以建模更复杂的轨迹。但在实践中S-LSTM的性能并没有像论文中所说的那样超越LSTM。SGAN-1V-1表现没有LSTM好的原因在于模型的条件输出只是多种可能的未来轨迹的其中之一，也许与真实标签有较大差异，但当我们考虑多样本时，SGAN表现超越了baseline方法，这从一个侧面证明了该模型具有很好的多模态输出性质。

- 性能速度

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201031829832.png" alt="image-20220103182926796" style="zoom: 67%;" />

SGAN-P的预测速度是S-LSTM块近16倍，LSTM更快但是不能避免碰撞和实现多模态输出预测。
