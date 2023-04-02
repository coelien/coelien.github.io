---
title: 论文阅读笔记：“ActionVLAD：Learning spatio-temporal aggregation for action classification”
tags: 
- Activity Recognition
categories:
- 论文阅读
- 行为识别
---

# ActionVLAD: Learning spatio-temporal aggregation for action classification

> 代码：http://rohitgirdhar.github.io/ActionVLAD

## 论文思想

**时空信息聚合**

![image-20221123105515330](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211231055372.png)

将特征空间划分为K个区域，该区域可以表示为“**action words**”，也可以称其为锚点(achor points ck)

公式：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211231056778.png" alt="image-20221123105611756" style="zoom: 50%;" />

上面的公式对特征与锚点（typical actions）之前的差异在整个视频维度进行了求和。

**why use VLAD to pool?**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211231103124.png" alt="image-20221123110326090" style="zoom: 67%;" />

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211231114829.png" alt="image-20221123111421804" style="zoom:50%;" />

**HOW to combine RGB and FLOW streams?**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211231111470.png" alt="image-20221123111110433" style="zoom:50%;" />



<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202211231113621.png" alt="image-20221123111334599" style="zoom: 80%;" />

## Ideas?

- can I add a attention to VLAD?
- where to add attention?
- can I add non-local to VLAD?

## Todos

- run the VLAD code
- look at the core codes where the VLAD is implemented
- try out the ideas that may work well