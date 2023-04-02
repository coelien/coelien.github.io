---
title: 论文阅读笔记：“Masked Autoencoders As Spatiotemporal Learners”
date: 2023-03-13
tags: 
- Activity Recognition
categories:
- 论文阅读
- 行为识别
---

# Masked Autoencoders As Spatiotemporal Learners

## 框架

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303130933640.png" alt="image-20230313093329487" style="zoom:50%;" />

- 从损坏的输入重建干净的信号

### 发现

- 最优mask比率和数据中的信息冗余度相关

- 使用更高的mask比率可以更好地利用视频的时序关联信息

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303130953989.png" alt="image-20230313095325708" style="zoom:50%;" />

### 采样方法

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303130956473.png" alt="image-20230313095626410" style="zoom:50%;" />

### 自编码

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303131006201.png" alt="image-20230313100659168" style="zoom: 50%;" />

- decoder结构与encoder相比是不对称的，且只在预训练进行视频重建的时候使用。其复杂度远远小于encoder，因而就算处理的是全部的patches，它也不是性能瓶颈。

## 实验

### 性能优势

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303131030932.png" alt="image-20230313103018906" style="zoom:50%;" />

> a masking ratio of 90% reduces the encoder time and memory complexity to *<*1*/*10

- 理论上， 7.7*×*  在计算量上的减少 *vs*. 编码全部tokens

### 精度优势

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303131040576.png" alt="image-20230313104009547" style="zoom:50%;" />

- 加入MAE能提升10个百分点以上，同时减少近1/5的训练时间

### 消融实验

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202303131044641.png" alt="image-20230313104453562" style="zoom:50%;" />

