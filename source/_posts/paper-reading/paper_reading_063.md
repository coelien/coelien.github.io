---
title: 论文阅读笔记：“Coherence Constrained Graph LSTM for Group Activity Recognition”
date: 2023-04-17
tags: 
- Group Activity Recognition
categories:
- 论文阅读
- 群体行为识别
---

# Coherence Constrained Graph LSTM for Group Activity Recognition  （TPAMI 2022）

> 主要思想：寻找群体体行为中的相关行为

## STCC和GCC约束

- STCC：如果一个个体行为在时间域上相互关联，并且在空间域上与其他个体行为具有一致性，我们称该种行为为相关行为
- GCC：如果一个特定的行为与整体群体行为一致性更高，那么它对群体行为的贡献也越高

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417095353737.png" alt="image-20230417095353737" style="zoom:50%;" />

## 网络框架

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417100222815.png" alt="image-20230417100222815" style="zoom: 50%;" />

## 核心模块

spatial interactions among persons are dependent on the temporal motions of individuals  

### CCG-LSTM

> 时空场景关联推理

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417103246814.png" alt="image-20230417103246814" style="zoom: 33%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417112848658.png" alt="image-20230417112848658" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417113033797.png" alt="image-20230417113033797" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417124514121.png" alt="image-20230417124514121" style="zoom:50%;" />

**temporal confidence gate** 

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417140102656.png" alt="image-20230417140102656" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417112107287.png" alt="image-20230417112107287" style="zoom:50%;" />

- 如果相差越小，值越接近1
- 加入这个temporal confidence gate后的新公式：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417124355537.png" alt="image-20230417124355537" style="zoom:50%;" />

**spatial context confidence gate** 

>  捕获空间域的个体的“relative”运动

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417140407896.png" alt="image-20230417140407896" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417140424319.png" alt="image-20230417140424319" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417140440639.png" alt="image-20230417140440639" style="zoom:50%;" />

- 如果相差越小，值越接近1
- 加入这个spatial confidence gate后的新公式：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417140728207.png" alt="image-20230417140728207" style="zoom:50%;" />

### AGG-LSTM

> 全局场景关联推理

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/image-20230417103201208.png" alt="image-20230417103201208" style="zoom:50%;" />

