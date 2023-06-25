---
title: 论文阅读笔记：“MGFN：Magnitude-Contrastive Glance-and-Focus Network for Weakly-Supervised Video Anomaly Detection”
tags: 
- video anomaly detection
categories:
- 论文阅读
- 视频异常检测
---

# 论文阅读笔记：“MGFN：Magnitude-Contrastive Glance-and-Focus Network for Weakly-Supervised Video Anomaly Detection”

## 网络框架

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230620161316631.png" alt="image-20230620161316631" style="zoom:50%;" />

## 核心亮点

### Feature Amplification Mechanism (FAM)  

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230620162407331.png" alt="image-20230620162407331" style="zoom:50%;" />

对于第i个视频的第t个片段，FAM会计算一个特征范数，他是在特征维度进行归一化

之后使用1d卷积调制范数信息来进行增强信息导出：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230620162955067.png" alt="image-20230620162955067" style="zoom:50%;" />

### Magnitude Contrastive Loss  

之前经典方案是二元交叉熵损失：$𝐿𝑠𝑐𝑒 =-𝑦 log(𝑠^{𝑖, 𝑗}) - (1 - 𝑦)log(1-𝑠^{𝑖, 𝑗}) $

论文提出了振幅对比损失：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230620163836165.png" alt="image-20230620163836165" style="zoom:50%;" />

p,q是正常clip的索引，u,v是异常clip的索引，Ma是异常视频top-k高的特征振幅。如果p，u是一对正常，异常视频clip

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230620164614861.png" alt="image-20230620164614861" style="zoom:50%;" />

总体loss计算公式：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230620165955867.png" alt="image-20230620165955867" style="zoom:50%;" />

