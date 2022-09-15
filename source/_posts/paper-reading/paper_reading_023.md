---
title: 论文阅读笔记：“Dynamic Image Networks for Action Recognition”
tags: 
- Dynamic Image
- rank pooling
categories:
- 论文阅读
- 行为识别
---

# Dynamic Image Networks for Action Recognition

> 这篇文章先后发表在了CVPR和TPAMI上，DI可以尝试作为新的输入模态来提高精度

## 核心思想

**动态图像**

> 它就是一个总结了表观和动态信息的标准RGB图像

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241606883.png" alt="image-20220624160600770" style="zoom:50%;" />

构建的思想为将视频表示为一个针对它的帧（$I_1,...,I_T$）的排名函数

从每一个单独的帧It中提取的特征向量表示为<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241553428.png" alt="image-20220624155342341" style="zoom:50%;" />，用Vt表示直到时间t这些特征的一个时序均值，表示为<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241556204.png" alt="image-20220624155638181" style="zoom:50%;" />，排名函数为每一个时间关联了一个分数<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241558566.png" alt="image-20220624155855542" style="zoom:50%;" />，这个d就是需要学习的参数，它反映了视频中帧的一个排名。而学习d是一个凸优化问题：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241601060.png" alt="image-20220624160132033" style="zoom:50%;" />

这个公式的本身意义很清楚：**越往后的帧会被赋予更大的得分**，公式中通过折页loss来表示，但要想在深度学习模型中使用，作者提出了一种近似方法。

作者不像之前论文中对局部特征的fisher vector编码去进行一个排名池化，而是之前应用于像素。于是提取的特征向量表示即为图像中每一个pixel每一个RGB通道的堆叠后的一个大向量。学习得到的参数**d***与普通的单帧具有相同数量的元素，并且可以被解释为一个标准RGB图像。动态图片实际上就是通过对整个视频中像素强度**沿时间维度进行集成和重排序**。

**动态特征图**

对动态图像的拓展，即我们可以对中间特征图进行rank pooling并得到动态特征图：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241640936.png" alt="image-20220624164022883" style="zoom:50%;" />

**近似排名池化 approximated rank pooling**

作者提出了的近似排名池化方法可以插入到CNN网络的中间层，并允许反向传播，提高了rank pooling的泛用性：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241626431.png" alt="image-20220624162625400" style="zoom:50%;" />

文章还提出了一种线性计算权重系数的方式：$\alpha_t=2t-T-1$，对比如下图所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241634624.png" alt="image-20220624163420581" style="zoom:50%;" />

## 实验效果

**ResNeXt-50消融实验**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241705932.png" alt="image-20220624170552882" style="zoom:50%;" />

**动态图像较视频序列均值图像等优越性**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241707941.png" alt="image-20220624170754912" style="zoom:50%;" />

**近似排名池化与排名池化的对比**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241814035.png" alt="image-20220624181423007" style="zoom:50%;" />

**动态图像是对静态图像的补充**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206241824153.png" alt="image-20220624182408126" style="zoom:50%;" />