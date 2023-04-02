---
title: 论文阅读笔记：“行为识别论文总结”
tags: 
- Activity Recognition
categories:
- 论文阅读
- 行为识别
---

# Paper Reading Reflections on Action Recognition

## 3D Convolutional Neural Networks（TPAMI 2013）

- 多通道输入（input），包括灰度，x方向梯度，y方向梯度，x方向光流，y方向光流信息

- 模型组合（architechture）

  捕获潜在的相互补充的信息

- 高层特征正则化（model）

  对每个类别预编码一个高层特征，期待学习到的特征与预编码特征尽可能地接近

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206171315716.png" alt="image-20220617131503637" style="zoom:50%;" />

- 可视化(experiment)

  对于没有正确分类的视频序列进行分析：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210091127809.png" alt="image-20221009112749598" style="zoom:50%;" />

## Large-scale video classification(cvpr 2014)

- 数据集(data)

- 计算效率(architechture)
  **双流法**: scene stream and centre stream

- 时空连接模式(model)
  对于时域信息，我们采用在网络的早期(early fusion)，后期(late fusion)，或是处理过程中(slow fusion)进行融合的效果

- 数据分析(experiment)

  **分类置信度bar图+对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209291637912.png" alt="image-20220929163715877" style="zoom: 67%;" />
  **类别分类精度对比排序**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209291637445.png" alt="image-20220929163759415" style="zoom: 50%;" />
  **filters可视化**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209291638359.png" alt="image-20220929163858259" style="zoom:50%;" />

## Learning Spatiotemporal Features（ICCV 2015）

- 模型可视化（experiment）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210091132320.png" alt="image-20221009113238201" style="zoom:33%;" />

- 对学习到的特征进行分析（experiment）

  - 紧凑性

    <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210091232918.png" alt="image-20221009123201880" style="zoom: 50%;" />

  - 区分能力(t-SNE)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210091232992.png" alt="image-20221009123223950" style="zoom:33%;" />



## Quo Vadis, Action Recognition(cvpr 2017)

- 数据集(data)：Kinetics

- 2D 卷积膨胀(model)

  从视频中学习时空特征的同时，利用优秀的ImageNet网络架构及参数

- 双流架构（model）

  双流算法采用的是 TV-L1 algorithm

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210081418878.png" alt="image-20221008141810793" style="zoom:50%;" />

- 数据分析（experiment）

  不同架构对比

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210081435564.png" alt="image-20221008143511527" style="zoom:50%;" />

## Pseudo-3D Residual Networks

- P3D Block: 3D卷积解耦（model）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111357498.png" alt="image-20220711135720380" style="zoom:50%;" />

- P3D Chain: 利用结构多样性（architecture）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207111405541.png" alt="image-20220711140537517" style="zoom:50%;" />

- 类别知识可视化（experiment）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101043699.png" alt="image-20221010104339492" style="zoom:50%;" />

## A Closer Look at Spatiotemporal Convolutions

- 在解耦3D Filter方面对P3D做了优化（model）

​	**R(2+1)D卷积块**

​	它将$N_i$个大小为$N_{i-1}*t*d*d$3D卷积核分解为$M_i$个大小为$N_{i-1}*1*d*d$的2D卷积核核$N_i$个大小为$M_i*t*1*1$的时间卷积核。因此$M_i$作为决定中间子空间（信号从空间卷积到时间卷积的投影）维度的超参。那么这个$M_i$该如何选择呢？

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207121045890.png" alt="image-20220712104556868" style="zoom:50%;" />

- mixed convolution（architecture）

​	**3D卷积在网络的早些层**，2D卷积在网络的后面的层

- 精度与复杂度对比（experiment）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131450799.png" alt="image-20220713145002760" style="zoom: 50%;" />

- 精度与输入帧数的关系（experiement）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131454109.png" alt="image-20220713145418069" style="zoom:50%;" />

​	Findings: 在较短帧上进行训练，在较长帧上进行finetuning，会达到比较好的效果。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207131505041.png" alt="image-20220713150536006" style="zoom:50%;" />

## Long-Term Temporal Convolutions

主要看它的实验部分：

- 数据增强实验

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101107691.png" alt="image-20221010110735658" style="zoom:50%;" />

- 模型组合

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101111787.png" alt="image-20221010111102751" style="zoom:50%;" />

- 学习到的filters可视化

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101119981.png" alt="image-20221010111919944" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101240656.png" alt="image-20221010124040579" style="zoom:50%;" />

## Non-local Neural Networks

- Non-local Block — 捕获长期时空依赖（model）

  理解non-local块的前提是理解非局部操作：

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101307449.png" alt="image-20220718092053605" style="zoom:50%;" />

  简单来讲，它计算的是第i个位置与其他所有位置的关系，进行求和与归一化后的值。在我的理解看来，$f(x_i,x_j)$就像是两个位置的关系进行建模，而$g(x_j)$是这个关系的权重。

  **二元函数f的选择**是论文讨论的重点：

  - 高斯: $f(x_i,x_j)=e^{x_i^Tx_j}$
  - 嵌入高斯: $f(x_i,x_j) = e^{\theta(x_i)^T\phi(x_j)}$
  - 点积: $f(x_i,x_j) = \theta(x_i)^T\phi(x_j)$
  - 连接: $f(x_i,x_j) = ReLU(w^T_f[\theta(x_i),\phi(x_j)])$

​	  文章还说明了**自注意力模块**与嵌入高斯版的非线性操作的异同，他们的最终形式是一样的：$y=softmax(x^TW^T_{\theta}W_{\phi}x)g(x)$。但是作者拓展得到时空非局部网络能够处理多样的输入，具有泛用性。

​	  作者将non-local块形式化地定义为：$z_i=W_zy_i+x_i$。实际上即使用了残差连接，它有诸多好处：

- 能够让我们将新的non-local块插入任何预训练模型但不改变他的任何行为，只需将$W_z$初始化为0即可。
- 而是可以增强特征（+）, 同时防止梯度消失。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207181000672.png" alt="image-20220718100033581" style="zoom: 33%;" />

- **加入non-local块的位置选择**

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101310914.png" alt="image-20220718124617098" style="zoom:50%;" />

  如图，加入到3，4 stage都是合适的

- **加入non-local块数的影响**

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101310607.png" alt="image-20220718125004600" style="zoom:50%;" />

  而且该实验也证明了精度的提升并不全部来自网络深度的增加

- **时间-空间-时空维度的non-local操作对模型的影响**

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101311174.png" alt="image-20220718125601152" style="zoom:50%;" />
