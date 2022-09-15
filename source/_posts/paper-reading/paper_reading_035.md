---
title: 论文阅读笔记：“Learning Actor Relation Graphs for Group Activity Recognition”
tags: 
- Group Activity Recognition
- ARG
- GCN
- relation reasoning
categories:
- 论文阅读
- 群体行为识别
---

# Learning Actor Relation Graphs for Group Activity Recognition(2019)

## 引言总结分析

组行为识别不仅需要描述场景中每个人地个体行为，还要推理出他们群体性地行为。所以对人与人之间的关联关系进行准确建模，并进行关系推理对于理解多人的群体行为是十分重要的。然而对这些人之间的关系进行建模是十分有挑战性的，因为我们只能够获取到个体行为标签和集体行为标签，对隐藏其下的相互作用信息没有足够的知识。论文提出可以通过其他方面：诸如表观相似性、相对位置来对人与人之间的关系进行推理。之后论文说目前提出的模型基本都是两阶段的模型，存在计算复杂度高，对于组行为的变化缺少灵活性等问题，针对这些问题论文提出了端到端的模型。与之前的模型不同，图的连接信息可以直接地从视频中学习得到。论文通过构建一个**角色关系图ARG**对人与人之间的关系进行建模。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251622813.png" alt="image-20220725162239685" style="zoom:50%;" />

如上图，图中的每一个结点代表每一个人的特征，图中的边表示两人之间的关系。

## 网络结构

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251629736.png" alt="image-20220725162932632" style="zoom:50%;" />

整体框架可以分为三步：首先，作者均匀地从视频中采样K帧，并从采样的帧中提取角色的多尺度特征图，应用RolAlign来提取每个角色bounding box的特征，将特征送入fc层以得到一个d维的表观特征向量。

之后，利用这些原始特征，论文构建了角色关系图。为了能表达多样的关系信息，作者对同一角色特征集合构建了多幅ARG。

最后，作者利用GCN来基于ARG进行关系推理，之后ARG会被融合起来以生成角色之间的关系表示，并通过分类器来分别识别个体和群体信息。

> ps. 我一开始理解错了，其实N不是场景内人的数量，而是所有帧中bounding box的数量

## 核心亮点

**构建ARG**

- 图的定义

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251651189.png" alt="image-20220725165130160" style="zoom:50%;" />

G中的每个位置$G_{ij}$其实表示的是j角色的特征对于i角色特征的重要性，上面的公式通过h函数来根据表观联系和位置关系计算边权

h函数的具体形式如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251651325.png" alt="image-20220725165155300" style="zoom:50%;" />

- 表观联系

  - 点积：

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251657686.png" alt="image-20220725165723662" style="zoom:50%;" />

  - 嵌入点积：

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251658801.png" alt="image-20220725165816777" style="zoom:50%;" />

  - 关系网络：

  <img src="C:/Users/sixwa/AppData/Roaming/Typora/typora-user-images/image-20220725165906429.png" alt="image-20220725165906429" style="zoom:50%;" />

  

- 位置关系

  - 距离掩膜：

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251700208.png" alt="image-20220725170017187" style="zoom:50%;" />

  - 距离编码：

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251700861.png" alt="image-20220725170043839" style="zoom:50%;" />

**时序建模**

- 稀疏时序采样
  - 数据集较小，容易过拟合
- late fusion

上面两种时序建模方式能够在尽可能少的花费下，尽量保持时序信息

**使用GCN进行关系推理**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251708724.png" alt="image-20220725170846703" style="zoom:50%;" />

其中，G就是前一步构建的ARG，W是权重矩阵，Z是输入，我们可以堆叠多个GCN层，为了效率论文中只使用了一层GCN。

**对多个图的推理结果进行融合**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251712108.png" alt="image-20220725171226073" style="zoom:50%;" />

论文其实就是简单的逐元素相加。当然我们也可以考虑concate，最大值融合，conv融合，逐点乘积融合等多种融合方案。

**损失函数**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207251716038.png" alt="image-20220725171623015" style="zoom:50%;" />

该损失包含了个体行为分类损失和群体行为分类损失

## 实验分析

### 消融实验

> 判断论文提出的框架的各个模块对最终的精度的影响

**不同表观联系函数**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207261415551.png" alt="image-20220726141528518" style="zoom:50%;" />

**不同位置联系函数**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207261415032.png" alt="image-20220726141555007" style="zoom:50%;" />

**不同数量ARG图**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207261416177.png" alt="image-20220726141613151" style="zoom:50%;" />

**不同ARG图融合方法**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207261416255.png" alt="image-20220726141630228" style="zoom:50%;" />

**不同时序建模方法**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207261416805.png" alt="image-20220726141649772" style="zoom:50%;" />

### state of arts 对比

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207261419440.png" alt="image-20220726141911401" style="zoom:50%;" />

### 可视化

**ARG图可视化**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207261420706.png" alt="image-20220726142027529" style="zoom:50%;" />

**t-SNE 可视化**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202207261420823.png" alt="image-20220726142039766" style="zoom:50%;" />
