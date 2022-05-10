---

title: AI-EARTH学习记录
tags: 
- nlp
- time-transformer
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

**训练器**

**包含变量**：

- 配置 config
- 设备  device
- 网络结构 network
- 优化器 opt

> optim作用：基于梯度更新当前的参数，具体地，其[`step()`](https://pytorch.org/docs/stable/generated/torch.optim.Optimizer.step.html#torch.optim.Optimizer.step)方法可以更新所有参数（要在梯度计算出来之后调用，例：loss.backward()）

**注意**，将model放入gpu在**构建优化器之前**

- 权重 weight

我们可以由下图得知，不同的月份有不同的权重，self.weight实际上是公式1.2中的$a*ln(i)$，a实际上是accskill权重，预报提取时间越长，accskill权重越高$(i\le4,a=1.5;5\le i \le11,a=2;12\le i \le18,a=3;19\le i ,a=4)$

<img src="C:/Users/sixwa/AppData/Roaming/Typora/typora-user-images/image-20220510151538633.png" alt="image-20220510151538633" style="zoom: 33%;" />

```python
self.weight = torch.from_numpy(np.array([1.5]*4 + [2]*7 + [3]*7 + [4]*6) * np.log(np.arange(24)+1)).to(configs.device)
```

**包含函数**：

- 得分函数score

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202205101504511.png" alt="image-20220510150436482" style="zoom:50%;" />

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202205101504154.png" alt="image-20220510150417127" style="zoom:50%;" />

  <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202205101502854.png" alt="image-20220510150206737" style="zoom: 50%;" />

```python
# 中心化
pred = y_pred - y_pred.mean(dim=0, keepdim=True)  # (N, 24)
true = y_true - y_true.mean(dim=0, keepdim=True)  # (N, 24)
# 协方差矩阵判断相关性，值越大相关性越高
cor = (pred * true).sum(dim=0) / (torch.sqrt(torch.sum(pred**2, dim=0) * torch.sum(true**2, dim=0)) + 1e-6)
acc = (acc_weight * cor).sum()
# rmse是所有预测月份的均方根误差
rmse = torch.mean((y_pred - y_true)**2, dim=0).sqrt().sum()
return 2/3. * acc - rmse
```

- sst损失函数

在经纬度上计算均方根误差，再在样本维度上求均值，再返回所有月份的和

```python
# (5400+5010,26,1,24,48)
rmse = torch.mean((y_pred - y_true)**2, dim=[2, 3])
rmse = torch.sum(rmse.sqrt().mean(dim=0))
return rmse
```

- nino损失函数

```python
# (5400+5010,24)
with torch.no_grad():
    rmse = torch.sqrt(torch.mean((y_pred - y_true)**2, dim=0)) * self.weight
return rmse.sum()
```

- 训练单步

需要三个**输入**：input_sst, sst_true, nino_true, ssr_ratio，将input_sst和sst_true作为source和target送入STTransformer进行处理，输出为sst_pred, nino_pred。

> 注：该模型虽然提供了两个Loss函数，但是只根据sst损失函数来更新梯度

在计算得到梯度之后，更新参数之前，代码使用了**梯度裁剪**的方法来防止梯度爆炸

> 注：梯度裁剪：如果梯度变得非常大，那么我们就调节它使其保持较小的状态
>
> <img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202205101550159.png" alt="img" style="zoom:33%;" />

训练单步的**返回**为sst损失, nino损失和nino_pred

- 测试

测试函数将测试数据数据送入网络得到了sst_pred, nino_pred，将他们存入了列表中，之后在样本维度将他们连接起来，这样的好处是损失计算是针对整个测试数据集的。

- 推理

在推理函数中使用了上面的test函数，可以直接计算损失：

```python
nino_true = torch.from_numpy(dataset.target_nino).float().to(self.device)
sst_true = torch.from_numpy(dataset.target_sst).float().to(self.device)
sc = self.score(nino_pred, nino_true)
loss_sst = self.loss_sst(sst_pred, sst_true).item()
loss_nino = self.loss_nino(nino_pred, nino_true).item()
```

推理函数的**返回**值即为loss_sst,loss_nino,sc

- 训练

训练时首先将准备好的训练集和验证集用dataloader封装一下我们的cmip_dataset，并设定其为随机采样。将最好的score设定为负的无穷大浮点数。在每一个epoch里，更新ssr比率，调用train_once得到我们的损失和nino预测。根据nino预测和真实值我们可以得到score，它不只是在每个epoch结束后进行evaluate而是每300个batch评估一次，以免最优点被错过，因为该模型的训练时间较短。训练还设计了patience机制，若多个epoch之后score未变好就直接结束训练。

- 保存config
- 保存模型ConvTTLSTM

- 训练入口main函数：

main做的都大同小异，首先读入数据，并划分为训练，验证和测试集，之后使用cmipdataset对训练集和测试集进行wrap，初始化训练器进行训练即可：

```python
trainer = Trainer(configs)
trainer.save_configs('config_train.pkl')
trainer.train(dataset_train, dataset_eval, 'checkpoint.chk')
```

#### 模型

##### 模型结构

论文设计的自注意力模块（Space-Time Attention）如下图所示，

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202205101637540.png" alt="image-20220510163737467" style="zoom:50%;" />

首先我们要定义一个SpaceTimeTransformer类，在其初始化函数中，首先保存其设置，设备，输入维度

**线性嵌入**

首先论文将clip分解为patches，用x来表示每一个patch，公式1进行了线性嵌入得到嵌入向量z，其中E和e都是可以学习的参数，e代表对每个patch的时空位置进行编码的位置嵌入向量。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204291136330.png" alt="image-20220429113605223" style="zoom:50%;" />

**查询键值计算**

模型包含了L个编码块，在每个块中，为每一个patch的表示（由上一个块编码得到）中计算查询/键/值向量。其中LN（）代表LayerNorm；a = 1,...,A，其中A为多头注意力的数量，其中每个注意力头的隐维度数量为Dh = D/A

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204291150047.png" alt="image-20220429115006018" style="zoom:50%;" />

**自注意力计算**

自注意力权重可以按照如下的公式进行计算：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204291204875.png" alt="image-20220429120419846" style="zoom:50%;" />

SM（）代表softmax激活函数，自注意力仅在p(空间维度)或t(时间维度)上进行计算所以计算量被显著地降低了。

**编码过程**

第l个块的patch编码z可以通过:

- 先计算值向量的加权和，公式如下：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204291223852.png" alt="image-20220429122304822" style="zoom:50%;" />

- 将这些向量沿HEAD维度进行连接，再传入多层感知机即可得到最终编码向量（注，在每一个操作中还使用了残差连接）：

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204291226426.png" alt="image-20220429122626393" style="zoom:50%;" />

