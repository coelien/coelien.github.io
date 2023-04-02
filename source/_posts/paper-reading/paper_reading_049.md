---
title: 论文阅读笔记：“Social Adaptive Module for Weakly-supervised Group Activity Recognition”
tags: 
- Group Activity Recognition
categories:
- 论文阅读
- 群体行为识别
---

# Social Adaptive Module for Weakly-supervised Group Activity Recognition

## 核心亮点

**SAM(Social Adaptive Module)**

对于弱监督GAR，SAM可以自适应地选择具有区分能力的边界框及关键帧。它利用了social 假设，即关键的实例（人或帧）总是高度关联的。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101440679.png" alt="image-20221010144046620" style="zoom:50%;" />

- 整体任务建模：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210101446885.png" alt="image-20221010144648865" style="zoom:50%;" />

其中，V1-Vt为输入的帧序列，D(Vt)代表每个帧对应的N个proposal。

- 空间建模函数 F(Vt;D(Vt);W)：

首先，对每个帧Vt提取卷积特征图，其次利用RoIAlign来提取个体特征，最后将所有个体特征融合为一个单独的帧级别的向量。在这里，F函数需要在空间域里选择K个具有区分能力的个体特征。

- 时间建模函数O()

我们希望O可以选择K个有效的帧级别的特征表示。

对于两个函数，我们可以进行抽象以得到一个统一的函数M，函数M的作用是学习向量$\lambda$，它是用来选择K个特征的

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210131903666.png" alt="image-20221013190317594" style="zoom:50%;" />

- 密集关系图

我们通过公式$g(x_i,x_j)=\Phi(x_i)^T\Psi(x_j)$来计算第i个特征和第j个特征之间的关系。由此，我们可以得到关系矩阵R。

- 剪枝操作

首先，我们通过公式$\alpha_i=\Sigma_{j=1}^N(r_{ij}+r_{ji})$计算每个特征的关联度。它是将每个特征的出边和入边进行了求和。如果值越大，说明这个特征的重要性越高。因此，我们根据计算结果可以选择Top K个特征：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210131916093.png" alt="image-20221013191657071" style="zoom:50%;" />

- 稀疏关系图

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210131918942.png" alt="image-20221013191847918" style="zoom:50%;" />

其中$\hat{R}$代表涉及选中特征的关系矩阵。从式中可以看出，我们对特征i到的K个特征进行了加权求和，文章称其为关系嵌入。

## 实验部分

- 消融实验

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210131937212.png" alt="image-20221013193707180" style="zoom:50%;" />

- 对比实验

可以与精度model进行比较

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202210131934875.png" alt="image-20221013193449830" style="zoom: 50%;" />