---
title: 论文阅读笔记：”BSRN“
tags: 
- 超分辨率
- DIV2K
categories:
- 论文阅读
---
# BSRN

## 核心方法

- RRB
  -  keep track of the status of the current image features

- 提高BSRN模型性能的方法：
  - 增加卷积通道宽度c
  - 增加block state的特征维度s--- make the model compact

## 实验部分

### 数据集

- 使用DIV2K来训练BSRN模型
- 使用四个基准数据集来评估模型性能： Set5 [1],Set14 [2], BSD100 [3], and Urban100 [4]

### 指标

用来检测生成的优化图像的质量

- PSNR
- SSIM

### 实验细节

- 本文建立了single-scale和multi-scale两个BSRN模型。

- singe-scale模型是用来：
  - 检验使用块状态的有效性
  - 探索频率控制变量r的作用

- multi-scale模型是用来：
  - 将本文模型同世界上最先进的方法进行比较

- recursive operations *R* 和 frequency control variable *r* 设置为16，1

- 在每一步训练使用8个训练patches，在数据增强时，patches被随机地翻转或旋转

- 检验使用块状态的有效性：

  - single-scale BSRN model having an upscaling factor of 4，evaluate on BSD100 dataset
  - *r* = 1
  -  without the block state：
    - *s* = 0
    -  *c* from 64 to 96
  - with block state:
    - c = 64
    - s from 1 to 64

  <img src="static\20210927paperreading00102.png" alt="20210927paperreading00102" style="zoom: 67%;" />

- 探索频率控制变量r的作用：它决定了upscaling part被应用的次数, which is R/r，evaluate on BSD100 dataset

  - upscaling part 计算复杂性来源：
    - 增加的卷积过滤器数量
    - depth-to-space操作
    -  increased spatial resolution
  - *c* = 64 and *s* = 64 

  <img src="static\20210927paperreading00103.png" alt="20210927paperreading00103" style="zoom:67%;" />

- 本文模型同世界上最先进的方法进行比较：

  - 在四个基准数据集上进行比较

  <img src="static\20210927paperreading00104.png" alt="20210927paperreading00104" style="zoom:50%;" />
