---
title: 群体行为识别论文总结
tags: 
- Group Activity Recognition
categories:
- 论文阅读
- 群体行为识别
---

# 2023.1.15汇报大纲

> 基于关键实例的多层次时空推理transformer的群体行为识别方法

## 研究背景





## 实验创新点相关

### 识别关键帧/关键角色

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301132245122.png" alt="image-20230113224543904" style="zoom: 50%;" />

**Detecting events and key actors in multi-person videos**

结合空间注意力和时间注意力

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301132306283.png" alt="image-20230113230651257" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301132304782.png" alt="image-20230113230458367" style="zoom:50%;" />

**Position-Aware Participation-Contributed Temporal Dynamic Model for Group Activity Recognition**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301140933320.png" alt="image-20230114093322289" style="zoom:50%;" />

![image-20230114094124755](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301140941878.png)

**Social Adaptive Module for Weakly-supervised Group Activity Recognition**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301140950015.png" alt="image-20230114095050950" style="zoom:50%;" />

这个筛选的时候未用到注意力机制，主要实在密集关系图上做剪枝，做剪枝区别于前面的注意力机制，将所有非关键帧及非关键角色剔除。



### 时空关系推理

**Empowering Relational Network by Self-Attention Augmented Conditional Random Fields for Group Activity Recognition**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301141114577.png" alt="image-20230114111412546" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301141122951.png" alt="image-20230114112250908" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301141121358.png" alt="image-20230114112147329" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301140947344.png" alt="image-20230114094713249" style="zoom:50%;" />

**HiGCIN: Hierarchical Graph-based Cross Inference Network for Group Activity Recognition**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301141009592.png" alt="image-20230114100951546" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301141032433.png" alt="image-20230114103252405" style="zoom:50%;" />

其实，它的计算本质和上面提及的注意力很像，加上softmax后，该模块等价于transformer中的自注意力模块。与之前的注意力所区别的是

1. CIN推理的特点是它只将视频中同一帧所有角色的特征，和同一角色所有帧的特征进行计算。
2. CIN计算的是其他特征和当前特征的pair-wise关系推理，而前面计算的是个体特征与群体行为之间的注意力权重。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301141105310.png" alt="image-20230114110529140" style="zoom: 33%;" />

**Spatio-Temporal Dynamic Inference Network for Group Activity Recognition**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301141129825.png" alt="image-20230114112900764" style="zoom:50%;" />

之前方法对个体之间的交互均在预定义的图上进行建模。这种方法可行，但有着很多缺陷：

- 与给定主体交互的那些客体应该是**主体特有的**，或者说**针对主体的**，而不应该是**预定义的**。而一个预定义的图并不适合所有人的关系推理。
- 之前预定义的图模型在一个全连接或是十字交叉的图上来对交互关系进行推理。它很容易导致**过平滑**(over-smoothing)，令特征难以区分。而且在长视频剪辑或是场景中有很多人的情况下，会导致极高的计算复杂度。

![image-20220728153718703](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207281537853.png)

**动态关系 Dynamic Relation**

这里的动态指的是，关系矩阵仅仅依赖于初始化的交互场中的特征。所依赖的特征不是固定的，而是动态变化的。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301141459159.png" alt="image-20220728161252942" style="zoom:50%;" />

其中，$W_a \in \R^{K\times(K\times D)}$，$u_i \in \R^{(K\times D)}$

上面是计算第i个特征关系矩阵的表达式。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207281616810.png" alt="image-20220728161612785" style="zoom:50%;" />

上面的特征更新表达式从形式上与ARG的图卷积层是基本一致的，区别主要在于范围一个是K，另一个是T*N。

**动态漫游 Dynamic Walk**

论文的目的是通过DW模块来对复杂的时空依赖进行建模，它只使用了大小局限的交互场。首先，对于给定的第i个个体特征，我们要预测交互场范围内的时空动态漫步偏移量$\Delta P_i=\{\Delta p_{i,k}|k=1,...,K\}=W_pu_i+b_p$，其中$W_p \in \R^{(K\times 2)\times(K\times D)}$ 为线性投影矩阵，$u_i$为所有交互场内堆叠起来的特征向量。得到漫步偏移量后，动态漫步的特征的计算公式如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207281640994.png" alt="image-20220728164025967" style="zoom:50%;" />

其中，$\Delta p_{i,k}$为第i个交互场的第k个特征的坐标。

**结合DR和DW**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207281641934.png" alt="image-20220728164121912" style="zoom:50%;" />

同过上式我们可以结合DR和DW来进行动态更新。

### 多层次语义场景推理

将整个场景按照粒度的不同进行划分，形成了不同尺度大小的场景元素。这些不同尺度元素存在着组合关系。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301042108663.png" alt="image-20230104210808551" style="zoom:50%;" />

GAR任务要解决的两个难题：

- 对于整个复杂场景做复合式理解
- 在所有场景元素上进行关系推理

使用key-point模态的好处：

- 对数据进行去隐私化，减少道德问题
- RGB输入对背景、光线亮度、以及纹理信息较为敏感，而key-point不会

**网络结构**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301042126928.png" alt="image-20230104212604869" style="zoom:50%;" />

**Multiscale Transformer Block**

多尺度transformer块可以层次式地对不同尺度的tokens进行关系推理。

- 人体关键点（key point）: $k^j_p \in \R^d$—第p个person第j个关键点的特征。人体关键点初始化包括：坐标嵌入，时间信息嵌入，关键点类型嵌入。
- 人（person）：$P_p \in \R^d$—对person关键点坐标在时间维度进行聚合，并作线性变换。
- P-to-P交互（interactions）：$I_i \in \R^d$—人p和人q之间的交互初始化为，$P_p 和 P_q$的连接，并作线性变换。
- 人群（person group）：$g_g \in \R^d$—对场景内的人进行聚合（使用k-means等算法）
- 剪辑（clip）：CLS是一个可学习的嵌入向量，它可以使得transformer可以从输入序列的所有tokens中总结分类相关的特征表示信息
- 对象（object）：这里特指球关键点$e_e \in \R^d$，因为它可以帮助我们更好地识别关键球员。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301042143880.png" alt="image-20230104214350814" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301091803030.png" alt="image-20230109180341985" style="zoom:50%;" />

由上图可知，multiscale transformer block在不同的尺度上有着差异化的输入，但是每个尺度的操作都是相同的。

**尺度一致的对比聚类**

我们强制同一clip的不同scale下的表示在聚类时所分配的标签是一致的，这其实是对特征空间的一种正则化处理。论文使用了交换预测（swapped prediction）机制来保证一致性。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301042123170.png" alt="image-20230104212303032" style="zoom:50%;" />

这种对比学习方法可以增强中间特征表示，从而提升总体效果。

- 假设$v _{n,s} \in \R^d$表示第n个剪辑的第s个scale下的clip特征，然后通过将v向量映射到k个可训练的原型向量$\{c_1,...c_k\}$中，来计算code $q_{n,s} \in \R^K$。假设是s,w是其中的两种尺度，并且我们希望从$v_w$预测$q_s$,并从$v_s$预测$q_w$，交换损失函数如下式：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301091827374.png" alt="image-20230109182735349" style="zoom:50%;" />

其中l函数计算匹配程度：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301091831701.png" alt="image-20230109183101676" style="zoom:50%;" />

总体的交换损失为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301091833620.png" alt="image-20230109183309578" style="zoom:50%;" />

关键实例的多层次时空推理transformer的群体行为识别方法(key Instance Multiscale ST transformer)

### 未来研究方向&优化

**KIMSTT网络**

![image-20230114163242036](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301141632130.png)

**改进思路**

1. 改进DIN模块，增加multiscale模块，使得可以对不同尺度范围的特征进行推理
2. 改进DIN模块，优化**动态漫游 Dynamic Walk**方法
3. 增加时空注意力模块，关注于与群体行为之间有较强的联系的个体特征