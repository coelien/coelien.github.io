---
title: 混合精度训练初探
tags: 
- 混合精度
- amp
categories:
- 华为项目
---
# 自动半精度（混合精度）训练

## 一. apex 与 amp

apex是英伟达构建的一个pytorch扩展，amp为其中提供混合精度的库

## 二. fp16的问题

### 2.1 数据溢出（下溢）

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204121831277.png" alt="image-20220412183129207" style="zoom: 67%;" />

### 2.2 舍入误差

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204121831120.jpeg" alt="preview" style="zoom: 50%;" />

## 三. 解决方法

### 3.1 FP32权重备份

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202204121835152.jpeg" alt="preview" style="zoom: 67%;" />

只有更新的时候采用F32

### 3.2 loss scale损失放大

根据链式法则，可以通过放大loss从而放大梯度来解决舍入误差

### 3.3 提高算数精度

**利用fp16进行乘法和存储，利用fp32来进行加法计算**，来减少加法过程中的舍入误差，保证精度不损失



## 四. 快速使用

```python
from apex import amp
model, optimizer = amp.initialize(model, optimizer, opt_level="O1",loss_scale=128.0) # 这里是“欧一”，不是“零一”
# loss.backward() becomes:
with amp.scale_loss(loss, optimizer) as scaled_loss:
    scaled_loss.backward()
```

opt_level 优先使用O2，若无法收敛则使用O1

如下是两个pytorch原生支持的apex混合精度和nvidia apex的loss scaler的具体实现：

```python
import torch

try:
    from apex import amp
    has_apex = True
    print("successfully import amp")
except ImportError:
    amp = None
    has_apex = False
    print("can not import amp from apex")


class ApexScaler:
    state_dict_key = "amp"

    def __call__(self, loss, optimizer, clip_grad=None, parameters=None, create_graph=False):
        with amp.scale_loss(loss, optimizer) as scaled_loss:
            scaled_loss.backward(create_graph=create_graph)
        if clip_grad is not None:
            torch.nn.utils.clip_grad_norm_(amp.master_params(optimizer), clip_grad)
        optimizer.step()

    def state_dict(self):
        if 'state_dict' in amp.__dict__:
            return amp.state_dict()

    def load_state_dict(self, state_dict):
        if 'load_state_dict' in amp.__dict__:
            amp.load_state_dict(state_dict)


class NativeScaler:
    state_dict_key = "amp_scaler"

    def __init__(self):
        # GradScaler对象用来自动做梯度缩放
        self._scaler = torch.cuda.amp.GradScaler()

    def __call__(self, loss, optimizer, clip_grad=None, parameters=None, create_graph=False):
        self._scaler.scale(loss).backward(create_graph=create_graph)
        if clip_grad is not None:
            assert parameters is not None
            self._scaler.unscale_(optimizer)  # unscale the gradients of optimizer's assigned params in-place
            torch.nn.utils.clip_grad_norm_(parameters, clip_grad)
        self._scaler.step(optimizer)
        self._scaler.update()

    def state_dict(self):
        return self._scaler.state_dict()

    def load_state_dict(self, state_dict):
        self._scaler.load_state_dict(state_dict)
```

apex + 分布式：

apex ddp默认使用当前设备，torch ddp需要手动指定运行的设备，用法和torch类似，

但需注意

```python
model, optimizer = amp.initialize(model, optimizer, flags...)
```

应在

```
model = apex.parallel.DistributedDataParallel(model)
```

之前

