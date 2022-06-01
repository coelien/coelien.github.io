---
title: 论文阅读笔记：”Action Recognition by Dense Trajectories“
tags: 
- 传统方法
- 光流法
categories:
- 论文阅读
- 传统方法
---

# Action Recognition by Dense Trajectories

## 生僻术语

- KLT tracker

用来抽取特征的一种方法，最早被提出是为了解决传统上的影像配准问题

- image registration：影像配准

- SIFT：尺度不变特征转换

用来侦测和描述影像中的局部性特征，它可以帮助辨识物体。基于物体上的一些局部外观的兴趣点而与影像的大小和旋转无关，对于光线，噪声的容忍度也很高

- optical flow field

光流（optical flow）用来描述相对于观察者的运动所造成的观测目标，表面或边缘的运动。它通过检测图像像素点的强度随时间的变化进而推断出物体移动速度及方向的方法

- feature

特征是一块关于图像内容的信息，主要包括图像的特定区域是否包含特定的属性。特征也许是图像中明确的结构，如点，边或是物体，它也可能是对图像进行特征检测后的结果

-  interest point

图像中类点的特征，他有局部二维结构，可以被鲁棒地检测

-  Gabor filter

它是用来进行纹理分析的线性过滤器

- median filter：中值滤波器

常用于去除图像或其他信号中的噪声

- bilinear interpolation：双线性插值

双线性插值是一种基本的重采样技术

- HOG：方向梯度直方图

用于目标检测的特征描述器。该技术是用来计算局部图像梯度的方向信息的统计值。**局部目标的表象和形状（appearance and shape）能够被梯度或边缘的方向密度分布很好地描述**

## Bag of Features approach	

> 该论文使用了该方法在行为分类的场景下评估了创新的视频描述方法

- Bag of Features框架是词包模型在图像识别领域的延申，2004年引入CV领域，逐渐形成了标准物体分类框架：

  - 底层特征提取
  - 特征编码
  - 特征汇聚
  - 使用SVM分类器进行分类

- 搭建Bag-of_Features的步骤：

  - 特征提取（SIFT算子）
  - K-means聚类
  - 量化特征，形成词袋
  - 统计每一类别的视觉单词出现频率，形成视觉单词直方图

  ![20210912paperreading00101](static\20210912paperreading00101.png)

  - 训练SVM分类器

- 具体代码实践参考[这里](https://www.cnblogs.com/skyfsm/p/8097397.html)

## 提取密集轨迹

- 计算 $P_{t+1}$ ：$P_{t+1} = (x_{t+1},y_{t+1}) = (x_t,y_t) + (M*\omega)|_{(\overline{x}_t,\overline{y}_t)}$

  - *M* is the median fifiltering kernel, ${(\overline{x}_t,\overline{y}_t)}$ is the rounded position of $(x_t,y_t)$

- 后续帧的点被连接起来形成轨迹$(P_t,P_{t+1},P_{t+2},...)$，为了提取密集光流，文章使用了Farneback算法

- 给定长度为L的轨迹，我们定义序列$S=(\Delta P_t,...,\Delta P_{t+L-1})$ 来描述它的形状，其中位移向量为$\Delta P_t = (P_{t+1}-P_t)$

- 规范化之后，我们可以得到轨迹描述器：
  $$
  S^`= \frac{(\Delta P_t,...,\Delta P_{t+L-1})}{\sum_{j=t}^{t+L-1}||\Delta P_j||}
  $$

## 轨迹对齐的描述器

- 计算局部描述器已成为视频表示的流行方法，本文在沿着轨迹的时空体（3D）中进行计算
  - 现存有很多行为识别的描述器

