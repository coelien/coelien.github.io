---
title: 论文阅读笔记：“Progressive Relation Learning for Group Activity Recognition”
tags: 
- Group Activity Recognition
- relation graph
- 强化学习
categories:
- 论文阅读
- 群体行为识别
---

# Progressive Relation Learning for Group Activity Recognition（cvpr2020）

## 核心思想

**个体特征提取**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209230941611.png" alt="image-20220923094137560" style="zoom:50%;" />

首先使用目标跟踪器得到人的边界框。之后对其使用卷积得到Person CNN，生成的空间视觉表示$x^{vis}_p$送入LSTM中得到个体的时间动态表示$x^{tem}_p$，使$x_p=[x^{vis}_p,x^{tem}_p]$来作为人的时空特征表示。

**语义关系图建立**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209230941470.png" alt="image-20220923094110438" style="zoom:50%;" />

定义无向图$G=(u,V,E)$，u是全局的行为分数，V是图的结点，E是图中的边。

- 场景信息聚合

对于每个结点i，收集并聚合其所有邻居j的信息：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209230930968.png" alt="image-20220923093021888" style="zoom:50%;" />

- 结点信息更新

对于每个结点i，使用刚刚得到的场景信息进行更新：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209230932016.png" alt="image-20220923093213994" style="zoom:50%;" />

- 边信息更新

对于每条边，使用更新后的结点信息进行更新：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209230933006.png" alt="image-20220923093321982" style="zoom:50%;" />

- 计算全局行为分数

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209230937648.png" style="zoom:50%;" />

因为在图中进行一次信息传播最多只能捕获成双的联系，为了编码高级别的交互，我们可以迭代m次进行图更新。



**渐进关系门控**

作者使用关系门控代理来探索一个自适应策略来选择与组行为相关的联系。决策过程可以用马尔可夫过程形式化表示。

**渐进特征蒸馏**

## 网络框架

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209211626908.png" alt="image-20220921162639769" style="zoom:50%;" />



![image-20220921163559011](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209211635151.png)

## 实验结果

**SOTA**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209231008033.png" alt="image-20220923100829986" style="zoom:50%;" />
