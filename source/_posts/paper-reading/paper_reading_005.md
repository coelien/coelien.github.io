---
title: 论文阅读笔记：”Social LSTM:Human Trajectory Prediction in Crowded Spaces“
tags: 
- 轨迹预测
- LSTM
categories:
- 论文阅读
- 轨迹预测
---
# 论文阅读笔记—Social LSTM:Human Trajectory Prediction in Crowded Spaces（CVPR 2016）

## 原理

简单来讲，该论文通过邻域池化的思想来综合多个行人轨迹的的信息，提供给LSTM模型来生成下一个隐变量，这个隐变量包含了自身的历史轨迹信息，也包含了其邻域LSTM的历史信息。这个问题的目的是预测密集行人的轨迹，来防止与人发生碰撞，是序列生成问题的一种变体。作者使用了带有社交池化的LSTM模型，希望对复杂的行人社交常识（避让，转向等）进行建模，并通过该模型解释聚集行人场景下的运动行为。

## 隐变量社交池化



<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201101457140.png" alt="image-20220110145702970" style="zoom:50%;" />

作者的思想很简单，通过共享周围邻域内的LSTM隐变量，使得模型具有捕获周围人运动信息的能力。在每一个时间步，LSTM单元从其领域LSTM单元接受池化隐状态信息，如下图所示，池化时，隐变量的空间信息可以在一定程度上保持。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201101503438.png" alt="image-20220110150338386" style="zoom: 50%;" />

下面的等式(1)即社交池化的形式化表达，它是一个（$n*n*D$）形状的3D张量，n代表邻域的范围，D为隐状态的维度。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201101504279.png" alt="image-20220110150417254" style="zoom:50%;" />

## 位置估计

论文里使用了t时刻的隐状态来预测t+1时刻人i轨迹位置的分布。作者假设其分布服从二元高斯分布，因其有5个待估计参数，可以利用线性层（权重矩阵shape为5*D）来估计，如等式4所示。我们可以通过该分布生成t+1时刻人i的轨迹位置。LSTM的参数可以使用似然函数损失作为目标函数来进行优化（即最小化负对数似然损失）。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202201101505559.png" alt="image-20220110150501520" style="zoom:50%;" />

