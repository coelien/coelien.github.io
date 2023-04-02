---
title: 论文阅读笔记：“Keeping Your Eye on the Ball”
date: 2023-03-13
tags: 
- Activity Recognition
categories:
- 论文阅读
- 行为识别
---

# Keeping Your Eye on the Ball: Trajectory Attention in Video Transformers（NIPS 2021）

> main idea: pooling along motion trajectories would provide a **more natural inductive bias** for video data, allowing the network to be invariant to camera motion.

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303201244278.png" alt="image-20230320124427201" style="zoom:50%;" />

首先预处理视频，生成ST个tokens，使用了cuboid嵌入来聚合不相交地时空块。之后在特征中加入了时间位置和空间位置编码，最后将可学习的分类token加入到整个序列中。

## video self-attention

**joint space-time attention**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303200935863.png" alt="image-20230320093506840" style="zoom:50%;" />

- 计算量是平方级别的$O(S^2T^2)$

**divided space-time attention（time transformer）**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303200943755.png" alt="image-20230320094305732" style="zoom:50%;" />

- 优点是计算量下降到$O(S^2T)$或$O(ST^2)$
- 模型分析时间和空间是独立的（不能同时对时空信息进行推理）
- 处理大量冗余信息，而且不灵活、不能充分利用有效tokens

**trajectory attention**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303200925591.png" alt="image-20230320092501493" style="zoom:50%;" />

- better able to characterize the temporal information contained in videos
- aggregates information along implicitly determined motion paths
- aims to share information along the motion path of the ball

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303200954992.png" alt="image-20230320095422970" style="zoom:50%;" />

- 应用在空间维度，且每个帧是独立的
- 隐式地寻找每个时刻轨迹的位置（通过比较query, key）

**trajectory aggregation**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303201009749.png" alt="image-20230320100927726" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303201010935.png" alt="image-20230320101056901" style="zoom:50%;" />

- 应用在时间维度
- 复杂度：$O(S^2T^2)$

**Approximating attention**

基于原型的注意力近似：

- 使用尽可能少的原型的同时重建尽可能准确的注意力操作
- 作者从queries, keys中选择最正交的R个向量作为我们的原型

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303201119293.png" alt="image-20230320111921268" style="zoom: 50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303201123844.png" alt="image-20230320112333825" style="zoom:50%;" />

因为最大复杂度为$R*D*N$，而R，D均为常数，所以可以认为复杂度从$O(N^2)降到了$$O(N)$

## 实验

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303201243287.png" alt="image-20230320124357224" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303201256377.png" alt="image-20230320125626322" style="zoom:50%;" />