---
title: 论文阅读笔记：“Empowering Relational Network by Self-Attention Augmented Conditional Random Fields for Group Activity Recognition（eccv 2020)”
tags: 
- Group Activity Recognition
categories:
- 论文阅读
- 群体行为识别
---

# Empowering Relational Network by Self-Attention Augmented Conditional Random Fields for Group Activity Recognition（eccv 2020)

## 核心思想

### 网络结构

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212091051449.png" alt="image-20221209105127373" style="zoom: 50%;" />

整体的网络可以分为两个阶段，第一阶段为特征提取网络，第二阶段为关系推理网络。appearence信息是通过faster rcnn+I3D得到的。其中，先使用faster rcnn得到检测框信息（它是在用预训练的coco模型在volleyball数据集上进行微调得到的）再用I3D提取特征，在这个过程中需要用到检测框信息（I3D是在预训练的kinetics模型在volleyball数据集上进行微调得到的）。场景信息即直接用I3D处理得到的视频特征。

### 创新模块

**temporal and spatial self-attention**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212091117878.png" alt="image-20221209111721845" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212091358472.png" alt="image-20221209135848443" style="zoom:50%;" />

空间注意力，尝试将整个图划分为不同大小的子图，对于不同规模的子图，计算其特征，对于这些进行加权平均，权重是可学习的。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212091359400.png" alt="image-20221209135924360" style="zoom:50%;" />

 **mean-field inference algorithm**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212121715757.png" alt="image-20221212171543661" style="zoom:50%;" />

简单地用图示来表示算法过程：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212121725870.png" alt="image-20221212172520845" style="zoom:50%;" />

**bidirectional universal transformer encoder** (UTE)

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212121717587.png" alt="image-20221212171751552" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212121718614.png" alt="image-20221212171815586" style="zoom:50%;" />

