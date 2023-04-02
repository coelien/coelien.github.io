---
title: 论文阅读笔记：“COMPOSER：Compositional Reasoning of Group Activity in Videos with Keypoint-Only Modality”
tags: 
- Group Activity Recognition
categories:
- 论文阅读
- 群体行为识别
---

# COMPOSER: Compositional Reasoning of Group Activity in Videos with Keypoint-Only Modality

### 主要思想

将整个场景按照粒度的不同进行划分，形成了不同尺度大小的场景元素。这些不同尺度元素存在着组合关系。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301042108663.png" alt="image-20230104210808551" style="zoom:50%;" />

GAR任务要解决的两个难题：

- 对于整个复杂场景做复合式理解
- 在所有场景元素上进行关系推理

使用key-point模态的好处：

- 对数据进行去隐私化，减少道德问题
- RGB输入对背景、光线亮度、以及纹理信息较为敏感，而key-point不会

##　网络结构

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

## 实验结果

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202301091836104.png" alt="image-20230109183617056" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302201250122.png" alt="image-20230220125033021" style="zoom:50%;" />

- 正则化多尺度表示
- 多尺度表示可以表达更多的信息，和更有效的尺度一致性
- 数据增强可以有效增加训练数据大小，注入有效噪声，使得泛化更容易
- 辅助预测可以帮助学习中间表示
- 多尺度transformer可以通过组合推理的方式从细粒度到粗粒度学习视频的高层信息表示
- 参数共享和只使用单一的encoder效果都不好，使用反向的encoder效果会更差。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302201301327.png" alt="image-20230220130147293" style="zoom:50%;" />

GroupFormer中的CSTT模块有５个transformers并且还对图像场景进行了处理，所以计算昂贵。如果只使用RGB模态，composer的计算量为127M，因为它只需建模3个scales。
