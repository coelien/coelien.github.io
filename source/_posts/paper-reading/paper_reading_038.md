---
title: 论文阅读笔记：“Dual-AI： Dual-path Actor Interaction Learning for Group Activity Recognition”
tags: 
- Group Activity Recognition
- transformer
- MAC-Loss
categories:
- 论文阅读
- 群体行为识别
---

# Dual-AI: Dual-path Actor Interaction Learning for Group Activity Recognition(cvpr 2022)



<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051352085.png" alt="image-20220905135208975" style="zoom: 67%;" />

## 引言分析

通常对于联合的空间和时序信息信息进行优化比较困难，因此目前GAR的方法往往对时空注意力进行解耦。如图所示，这篇文章提出解耦的顺序对于不同的分类类别影响较大。基于这个发现，作者针对GAR提出了独特的双路角色交互框架，它可以有效地对两种互补的时空观点进行集成，从而能够学习视频中复杂的角色关联。此外作者提出了一个创新的损失函数：MAC-Loss（多尺度角色对比损失）。它可以提供一个简洁但有效的自监督信号，以增强两个通路之间的角色一致性。

## 核心亮点

**Dual-AI框架**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051411714.png" alt="image-20220905141136648" style="zoom: 67%;" />

Dual-AI包含了时间空间交互通路(TS)和空间时间交互通路(ST)。其中的基本单元（spatial actor transformer & temporal actor transformer）可以用来描述空间和时间联系。

- spatial actor transformer

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051424067.png" alt="image-20220905142434044" style="zoom:50%;" />

第一个式子加入了空间位置编码，以加入场景中角色的空间结构信息。第二个式子使用了多头自注意力机制(MHSA)来对场景中的角色位置信息的交互进行推理。第三个式子加入了前馈网络来进一步增加计算单元的学习能力。

- temporal actor transformer

与空间不同，时间trans的输入是$X^n \in \R^{K \times C}$，并且使用的是时间位置编码(TPE)

有了这些基本单元，我们就可以用他们来构造角色演变的时空表示，构造方法如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051436019.png" alt="image-20220905143620991" style="zoom:50%;" />

**MAC-Loss**

MAC-Loss可以通过帧-帧、帧-视频、视频-视频三个级别的**角色一致性**来有效增强特征辨别能力。它可以将两个相对独立的通路之间的合作进行增强

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051443904.png" alt="image-20220905144320862" style="zoom:50%;" />

- 帧-帧角色对比损失

对于一个通路上的帧表示，它应该与另一条通路上的帧表示相类似，但是与那条通路上的其他帧表示相区别。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051456522.png" alt="image-20220905145631495" style="zoom:50%;" />

- 帧-视频角色对比损失

对于一个通路上的帧表示，它应该与另一个通路上的对应角色的视频表示相一致，与那条通路上其他角色视频表示相区别

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051502432.png" alt="image-20220905150222409" style="zoom:50%;" />

- 视频-视频角色对比损失：$L_{vv}=-log\frac{h(\hat{X}^n_{TS},\hat{X}^n_{ST})}{\sum^N_{i=1}h(\hat{X}^n_{TS},\hat{X}^i_{TS})}$
- 最终的MAC-Loss为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051503355.png" alt="image-20220905150316331" style="zoom:50%;" />

- 训练目标：

  该框架可以端到端地对个体行为和组行为进行预测，通过结合交叉熵损失，最终的分类损失为：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051514801.png" alt="image-20220905151438775" style="zoom:50%;" />

最终作者结合所有的损失来训练他们的Dual-AI框架：$L=L_{cls}+L_{MAC}$；推理时，对两个通路的预测进行加权平均即可。

## 实验结果

**SOTA对比**

- volleyball数据集

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051527081.png" alt="image-20220905152721039" style="zoom:50%;" />

- collective数据集

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051529537.png" alt="image-20220905152931502" style="zoom:50%;" />

**弱监督场景**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051531733.png" alt="image-20220905153137698" style="zoom:50%;" />

**少样本场景**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051533887.png" alt="image-20220905153334856" style="zoom:50%;" />

**消融实验**

- 双通路设置

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051534144.png" alt="image-20220905153444116" style="zoom:50%;" />

- MAC-Loss

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051536423.png" alt="image-20220905153654396" style="zoom:50%;" />

- 场景信息

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051538312.png" alt="image-20220905153854288" style="zoom:50%;" />

**可视化**

- 组特征可视化

![image-20220905154120102](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051541161.png)

- 时空角色注意力可视化

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202209051548046.png" alt="image-20220905154821934" style="zoom:50%;" />
