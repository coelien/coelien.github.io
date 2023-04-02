---
title: 论文阅读笔记：“Position-Aware Participation-Contributed Temporal Dynamic Model for Group Activity Recognition”
tags: 
- Group Activity Recognition
categories:
- 论文阅读
- 群体行为识别
---

# Position-Aware Participation-Contributed Temporal Dynamic Model for Group Activity Recognition

## 主要思想

**Who are the key participants?**

作者认为关键参与者或者在整个过程中有稳定的运动，或者在某一时刻有显著的运动，如图所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051052604.png" alt="image-20221205105218498" style="zoom:50%;" />

## 网络框架

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051055522.png" alt="image-20221205105546410" style="zoom:33%;" />

**Long motion**

计算每个个体的平均运动强度 （MI）：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051104697.png" alt="image-20221205110403669" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051104618.png" alt="image-20221205110416594" style="zoom:50%;" />

**Flash motion**

因为快速运动经常随着时间推移而改变，所以设置注意力权值，可以衡量每个个体快速运动的强度。

> 动作变化最快的人，一定对整体的行为起着关键性作用吗？

文章也说，不是所有快速运动都与群体行为或是其他人相联系，例如碰撞，摔倒等动作。所以某一个特定的快速运动的人往往在语义上与群体行为或是其他个体有着更强的联系。而对于这个联系的计算，作者引入了PIM模块。

**PIM模块**

- 同时考虑**空间位置信息和特征相似度**来对关系进行推理。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051357418.png" alt="image-20221205135751357" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051343742.png" alt="image-20221205134337718" style="zoom:50%;" />

- Rx是特征交互函数：判断两个特征之间的相似性
- Rp是位置交互函数：基于位置信息衡量两个人之间交互的似然性
- K：场景中的所有人数

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051350680.png" alt="image-20221205135030659" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051353184.png" alt="image-20221205135349160" style="zoom:50%;" />

- d*是场景中距离最远的两人间的距离

**AGG-LSTM**

- 通过随时间变化的注意力，关注那些与群体行为关系密切的快速运动的人

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051358658.png" alt="image-20221205135822576" style="zoom:50%;" />

论文首先将场景中的个体分为不同组，对于volleyball数据集来说，组即是每只队伍。对于第g个组，开始id和结束id计算公式如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051402859.png" alt="image-20221205140207839" style="zoom:50%;" />

划分为不同组之后，我们就可以捕获快速运动对于群体行为的关系。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051406850.png" alt="image-20221205140657827" style="zoom:50%;" />

- $\hat{x}^f_t$：全部人的行为的隐变量表示
- $\alpha^*$可以强调与组行为联系更紧密的个体行为，并抑制非重要个体行为

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051414330.png" alt="image-20221205141447305" style="zoom:50%;" />

每个组的特征即可表示为LSTM输出的最后一个特征向量$\tilde{h}^{E_g}_*$，由公式也可看出，它聚合的是组内所有个体的信息。其实，每个组内也是有顺序的，这个顺序即是按Long Motion计算的MI递减顺序。最后，对所有组的特征进行拼接即可得到群体行为特征：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202212051422894.png" alt="image-20221205142258872" style="zoom:50%;" />