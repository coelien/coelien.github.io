---
title: AI-EARTH学习记录
tags: 
- nlp
- transformer
- AI-EARTH
categories:
- nlp项目
---

# AI-EARTH项目

## 背景

> **2021 “AI Earth”人工智能创新挑战赛**:[链接](https://tianchi.aliyun.com/competition/entrance/531871/introduction)

发生在热带太平洋上的厄尔尼诺-南方涛动(ENSO)现象是地球上最强、最显著的年际气候信号。准确预测ENSO，是提高东亚和全球气候预测水平和防灾减灾的关键。

## 任务

基于历史气候观测和模式模拟数据，利用T时刻过去12个月(包含T时刻)的时空序列（气象因子），构建预测ENSO的深度学习模型，预测未来1-24个月的Nino3.4指数

## 模型

### STTransformer

#### 训练

**学习率衰减策略**

> 训练初期学习率大一些，使得网络收敛迅速，在训练后期学习率小一些，可学习该[post](https://zhuanlan.zhihu.com/p/93624972?utm_source=qq&utm_medium=social&utm_oi=799204418460459008)了解更多

STTransformer采用了Noamopt优化策略，详细可参考Annotated Transformer, Harvard NLP Group, [ArdalanM/annotated-transformer](http://nlp.seas.harvard.edu/annotated-transformer/)，衰减公式如下所示：

![image-20220508113623206](https://raw.githubusercontent.com/coelien/image-hosting/master/img/202205081136303.png)





### ConvTTLSTM

#### 训练



