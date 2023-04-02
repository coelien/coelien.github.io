---
title: 论文阅读笔记：“Joint Learning of Social Groups, Individuals Action and Sub-group Activities in Videos”
tags: 
- Group Activity Recognition
- CIB
- 时空共时依赖
categories:
- 论文阅读
- 群体行为识别
---

# Joint Learning of Social Groups, Individuals Action and Sub-group Activities in Videos（eccv 2020）

## 引言分析

在一个真实场景中，它可能会包含多个人，每个个体可能属于一个社交群体中。文章专注于这样一个问题：“**谁属于哪个社交群体，并且这个群体在做些什么？**”。在此之前，有很多工作研究基于场景内的所有角色判断其群体行为，也有一些工作，基于场景内所有个体之间的联系进行分组推理。作者基于这两种工作，并进行了有效结合。这篇文章的主要贡献在于，提出了一种多任务解决方法，它可以同时解决分组，识别个体行为以及识别社交群体行为任务。

## 核心亮点

**GAR框架**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209171519088.png" alt="image-20220917151934037" style="zoom: 80%;" />

该框架包含三个部分：

- I3D backbone

  文章用最后一层输出作为全局视频表示，使用中间层mixed-4f的输出来进行个体特征的提取

- 自注意力模块

  使用非局部化操作，对个体特征图的空间域进行attention，以找到key-points。

- 图注意力模块

  GAR框架核心模块。该模块采用图注意力网络，主要作用是进行节点间的关系信息编码。

其训练是端到端的，目标函数为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209191004219.png" alt="image-20220919100434164" style="zoom:50%;" />

**社交行为识别**

社交行为的粒度介于个体行为与群体行为之间。文章对图注意力模块进行了简单修改，以适应新的任务。如下式，作者在目标函数中加入了边损失Lc。它可以让自注意力策略汇聚到个体之间的社交连接上。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209191012906.png" alt="image-20220919101254878" style="zoom:50%;" />

$L_{sgp}$表示每个社交群体的分类损失。在推理的时候，作者基于GAT学习得到的注意力分数，利用图谱聚类算法来实现社会群体的划分。

**增强数据集CAD**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209191022913.png" alt="image-20220919102207879" style="zoom:50%;" />

## 实验结果

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209191028186.png" alt="image-20220919102841069" style="zoom:50%;" />



**SOTA**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209191050830.png" alt="image-20220919105020783" style="zoom:50%;" />

**消融实验**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209191044370.png" alt="image-20220919104439335" style="zoom:50%;" />
