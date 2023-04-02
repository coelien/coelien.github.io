---
title: 论文阅读笔记：“Hunting Group Clues with Transformers for Social Group Activity Recognition”
tags: 
- Group Activity Recognition
categories:
- 论文阅读
- 群体行为识别
---

# Hunting Group Clues with Transformers for Social Group Activity Recognition

## 网络框架

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302271020733.png" alt="image-20230227102012606" style="zoom:50%;" />

**Deformable trans encoder**

使用修改过的特征图$Z_p$和位置编码P作为输入，一起送入堆叠的transformer encoder中， 可以得到优化的多尺度特征图。位置编码P辅助注意力模块识别特征在特征图的位置。

**Deformable trans decoder**

使用优化过的特征图$Z_e$和查询嵌入Q作为输入，一起送入堆叠的transformer decoder中，可以得到聚合的社群体特征图。查询嵌入Q被设计用来只能最多包含一个社会性群体。

**Prediction heads**

使用聚合后的特征图$H$送入检测头中进行预测。对于个体，设置了Box头和action头来对个体的位置和行为进行预测；对于社会群体，设置了size头，point头以及activity头来分别预测群体的人数，群体的成员bounding box中心点，以及群体行为类别。

## 损失函数

**Hungarian algorithm**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302271254422.png" alt="image-20230227125442388" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302271256605.png" alt="image-20230227125644581" style="zoom: 67%;" />

**Loss function**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202302271258244.png" alt="image-20230227125800212" style="zoom: 50%;" />