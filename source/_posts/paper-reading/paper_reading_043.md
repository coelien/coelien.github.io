---
title: 论文阅读笔记：“VATT： Transformers for Multimodal Self-Supervised Learning from Raw Video, Audio and Text”
tags: 
- transformer
- 多模态
- 自监督
categories:
- 论文阅读
- 行为识别
---

# VATT: Transformers for Multimodal Self-Supervised Learning from Raw Video, Audio and Text（nips 2021）

## 核心亮点

**VATT整体框架**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131355805.png" alt="image-20220913135534745" style="zoom:50%;" />

上图是VATT的整体架构，作者将每个模态输入到一个标记化层，在该层中原始输入被投影为一个嵌入向量，并在之后送入transformer中。作者使用了两种设置进行对比：

- 对于每种输入，其backbone都是分离的，权重不共享
- 使用单一骨干网络，共享不同模态的权重

网络提取模态特定的表示，并将它们映射到公共空间以通过对比损失相互比较

**DropToken**

因为transformer的计算复杂度是平方级别的，这个方法可以有效减少计算复杂度。我们得到标记序列后，随机采样其中的一部分，然后将采样后的序列送入transformer。对于可能包含冗余的数据，该方法可以大幅降低训练时间的同时保持很好的效果。

**标记化及位置编码**

作者将整个视频剪辑进行切分，每个patch的大小为$t \times h\times w\times3$。并在其上应用线性投影以得到d维向量表示。为了编码这些patches的位置，作者定义了如下可学习嵌入的特定维度的序列：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131417895.png" alt="image-20220913141736868" style="zoom:50%;" />

对于音频和文字的处理同理。transformer的输入如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131431524.png" alt="image-20220913143135501" style="zoom:50%;" />

$x_n$为输入patches序列，$x_{AGG}$为一种特殊的聚合标记的可学习嵌入，它即是整个输入序列的聚合表示。对于文字模型，作者移除了位置编码，并在多头注意力模块的第一层的注意力分数中加入了可学习的相对偏置，这个改变可以使得提出模型的权重能够直接迁移到最先进的语言模型T5上。

**公共空间投影**

给定视频-音频-文字三元组，作者定义了一个语义层次式公共空间映射，通过它我们可以利用余弦相似度来直接比较视频-音频对，视频-文字对。作者假设这些模态直接有不同级别的语义粒度，所有他定义了不同级别的投影：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131448362.png" alt="image-20220913144804337" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131451399.png" alt="image-20220913145132365" style="zoom:50%;" />

**多模态对比学习**

作者使用噪声对比估计(NCE)来对视频-音频对进行对齐，使用多实例学习NCE来对齐视频-文字对。正样本为采样视频中同一个位置的对应的模态信息流，负样本为视频中不匹配位置的采样样本。损失函数如下式所示：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131509663.png" alt="image-20220913150917623" style="zoom:50%;" />

其中N中包含了所有的负样本。式5中，P包含了5个离视频剪辑时间上最近的文字剪辑。$\tau$用来调整将正样本与负样本相区分的严格程度。总体的目标函数如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209131517359.png" alt="image-20220913151735334" style="zoom:50%;" />

## 实验结果

**action recognition**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209140929942.png" alt="image-20220914092920837" style="zoom:50%;" />

值得注意的是，如果从头开始训练（没有预训练），其精度会大幅下降

**可视化**

![image-20220914094415366](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209140944423.png)

![image-20220914094425965](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209140944030.png)
