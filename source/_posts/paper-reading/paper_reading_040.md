---
title: 论文阅读笔记：“GroupFormer： Group Activity Recognition with Clustered Spatial-Temporal Transformer”
tags: 
- Group Activity Recognition
- transformer
- CSTT
- 聚簇注意力机制
categories:
- 论文阅读
- 群体行为识别
---

# GroupFormer: Group Activity Recognition with Clustered Spatial-Temporal Transformer(ICCV 2021)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209070947338.png" alt="image-20220907094726265" style="zoom:50%;" />

## 引言分析

论文首先说明利用个体关联来推理集体行为是很有挑战性的。有很多方法也在尝试去捕获这种关联信息。近期的很多方法都用到了注意力机制来对个体关联进行建模。但是上述方法也存在缺陷：

1. 无法对时空情景信息作为一个整体来进行建模。

   如上图所示，时间信息于空间信息是强关联的，因此需要将它们作为一个整体来进行考虑

2. 没有基于他们直接的关联关系进行分组

   不是所有的角色都对事件的类别起作用，存在一部分关键角色 ，他们的贡献更多。

## 核心亮点

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209071021137.png" alt="image-20220907102136060" style="zoom:50%;" />

**组特征生成器GRG**

该模块是用来初始化组表示的。这个模块结合了场景特征和个体特征，其想法是通过visual tokens来总结视频中的信息，分别得到场景token和个体token，再将他们融合得到最终的组特征表示。

**聚簇时空transformer CSTT**

如图，STT包含了两个encoder并行化地生成时间和空间特征。之后两个decoders通过一种交叉的方式来对时间和空间特征进行解码。最后使用一个group解码器来增强组特征表示。

- 编码器：

我们使用encoders来嵌入时间和空间情景信息。公式如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209071433937.png" alt="image-20220907143320909" style="zoom:50%;" />

最终空间编码器得到的输出大小为$V_s \in \R^{T\times N \times D}$，同理可得出时间编码器的输出$V_t \in \R^{N\times T \times D}$。

- 解码器

个体解码器是用来将空间和时间情景信息整体地进行考虑。最终两个解码器的输出融合得到了增强的个体表示$X_I$。

组解码器利用增强的个体表示来强化组特征。在实际中，STT模块可以进行多次堆叠，以得到最佳的建模效果。

- 聚簇注意力机制

STT使用了全连接的注意力机制，但是这样其实计算了很多冗余的联系。因此为了关注于比较关键的组联系。作者设计了聚簇注意力块，该模块可以对个体进行聚簇，并利用组内和组间联系，从而大大降低了计算量。具体来说，他们定义了C个质心向量（簇中心）。对于组间联系可以用簇中心向量$M \in \R^{C \times D}$来代表。

## 实验结果

**SOTA**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209080935163.png" alt="image-20220908093531083" style="zoom:50%;" />

单单是使用rgb输入，性能效果就比之前的大部分方法好了。

**消融实验**

- 时空关系建模的效果

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209080939284.png" alt="image-20220908093914253" style="zoom:50%;" />

- 聚簇方法的效果

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209080941240.png" alt="image-20220908094113206" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209080941887.png" alt="image-20220908094135853" style="zoom:50%;" />

- 堆叠的CSTT块数量的效果

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209080943165.png" alt="image-20220908094308130" style="zoom:50%;" />

**可视化**

- t-SNE

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209080945816.png" alt="image-20220908094553747" style="zoom:50%;" />