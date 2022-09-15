---
title: Pytorch Ligntning 轻量级框架浅析
tags: 
- 轻量级框架
- lightning
categories:
- kinetics项目
---

# Pytorch Ligntning 轻量级框架浅析

> 该文章为基于[官方文档](https://pytorch-lightning.readthedocs.io/en/stable/starter/introduction.html)的学习总结

## 使用该框架的优点（why not using it?）

- 保持了全部的灵活性
- 更可读，将工程代码和研究代码解耦
- 更容易重现（reproduce）
- 更易扩展，且不需要改变模型

## 使用流程

### 定义 LightningModule

**SYSTEM VS MODEL**

> 一个lightning 模块不仅仅只是model，更是一个**系统**

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202206151727525.png" alt="img" style="zoom:50%;" />

实际上lightning模块仅仅是一个`torch.nn.Module`模块，该模块将所有的**研究代码集中到了一个文件当中**，使它包含了：

- The Train loop
- The Validation loop
- The Test loop
- The Prediction loop
- The Model or system of Models
- The Optimizers and LR Schedulers

通过Hooks特性，我们自定义**训练的任何细节**，详见：[Hooks](https://pytorch-lightning.readthedocs.io/en/stable/common/lightning_module.html#lightning-hooks)

**FORWARD vs TRAINING_STEP**

lighting推荐将训练和推理相分离

- 使用`forward`进行推理或预测
- 使用`training_step`进行训练

### 使用Lightning Trainer来拟合数据

- 首先需要定义数据集：

  ```python
  data_module = UCF101DataLoader()
  ```

- 初始化lightning模块和trainer，之后调用fit进行训练：

  ```python
  classification_module = VideoClassificationLightningModule()
  trainer = pytorch_lightning.Trainer(gpus=[0, 1], strategy="ddp", max_epochs=30,default_root_dir="logs_ucf101", precision=16)
  
  trainer.fit(classification_module, data_module)
  ```

- trainer支持多种训练功能的自动化
  - Epoch and batch iteration
  - `optimizer.step()`, `loss.backward()`, `optimizer.zero_grad()` calls
  - Calling of `model.eval()`, enabling/disabling grads during evaluation
  - [Checkpoint Saving and Loading](https://pytorch-lightning.readthedocs.io/en/stable/common/checkpointing.html)
  - Tensorboard (see [loggers](https://pytorch-lightning.readthedocs.io/en/stable/common/loggers.html) options)
  - [Multi-GPU](https://pytorch-lightning.readthedocs.io/en/stable/accelerators/gpu.html#multi-gpu-training) support
  - [TPU](https://pytorch-lightning.readthedocs.io/en/stable/accelerators/tpu.html)
  - [16-bit precision AMP](https://pytorch-lightning.readthedocs.io/en/stable/advanced/precision.html#amp) support

## 基本特色

### 自动化优化

只要在`train_step（）`返回loss损失，lighting就会自动地帮我们反向传播，更新优化器等；对于GAN，强化学习这类涉及多个优化器的模型，我们也可以关闭自动优化自己控制：

```python
def __init__(self):
    self.automatic_optimization = False
def training_step(self, batch, batch_idx):
    # access your optimizers with use_pl_optimizer=False. Default is True,
    # setting use_pl_optimizer=True will maintain plugin/precision support
    opt_a, opt_b = self.optimizers(use_pl_optimizer=True)

    loss_a = self.generator(batch)
    opt_a.zero_grad()
    # use `manual_backward()` instead of `loss.backward` to automate half precision, etc...
    self.manual_backward(loss_a)
    opt_a.step()

    loss_b = self.discriminator(batch)
    opt_b.zero_grad()
    self.manual_backward(loss_b)
    opt_b.step()
```

### 预测和部署

**进行预测的三种方式**

- 提取子模型：

  ```python
  # ----------------------------------
  # to use as embedding extractor
  # ----------------------------------
  autoencoder = LitAutoEncoder.load_from_checkpoint("path/to/checkpoint_file.ckpt")
  encoder_model = autoencoder.encoder
  encoder_model.eval()
  
  # ----------------------------------
  # to use as image generator
  # ----------------------------------
  decoder_model = autoencoder.decoder
  decoder_model.eval()
  ```

- 使用forward函数：

  ```python
  # ----------------------------------
  # using the AE to extract embeddings
  # ----------------------------------
  class LitAutoEncoder(LightningModule):
      def __init__(self):
          super().__init__()
          self.encoder = nn.Sequential(nn.Linear(28 * 28, 64))
  
      def forward(self, x):
          embedding = self.encoder(x)
          return embedding
  
  
  autoencoder = LitAutoEncoder()
  embedding = autoencoder(torch.rand(1, 28 * 28))
  ```

- 生产（production）:

  - Onnx using `to_onnx()` method

  ```python
  autoencoder = LitAutoEncoder()
  input_sample = torch.randn((1, 28 * 28))
  autoencoder.to_onnx(file_path="model.onnx", input_sample=input_sample, export_params=True)
  ```

  - TorchScript using `to_torchscript()` method.

  ```python
  autoencoder = LitAutoEncoder()
  autoencoder.to_torchscript(file_path="model.pt")
  ```

### 多种加速方式（accelerators）

- CPU

  ```python
  # train on CPU / 什么都不设置，默认在cpu上
  trainer = Trainer()
  # train on 8 CPUs
  trainer = Trainer(accelerator="cpu", devices=8)
  # train on 128 machines，8 devices per machine
  trainer = pl.Trainer(accelerator="cpu", devices=8, num_nodes=128)
  ```

- GPU

  ```python
  # train on 1 GPU
  trainer = pl.Trainer(accelerator="gpu", devices=1)
  
  # train on multiple GPUs across nodes (32 GPUs here)
  trainer = pl.Trainer(accelerator="gpu", devices=4, num_nodes=8)
  
  # train on gpu 1, 3, 5 (3 GPUs total)
  trainer = pl.Trainer(accelerator="gpu", devices=[1, 3, 5])
  
  # Multi GPU with mixed precision
  trainer = pl.Trainer(accelerator="gpu", devices=2, precision=16)
  ```

- TPU

- IPU

### 模型checkpoint

**保存训练超参**

```python
class MyLightningModule(LightningModule):
    def __init__(self, learning_rate, *args, **kwargs):
        super().__init__()
        self.save_hyperparameters()


# all init args were saved to the checkpoint
checkpoint = torch.load(CKPT_PATH)
print(checkpoint["hyper_parameters"])
# {"learning_rate": the_value}
```

使用self.save_hyperparameters()会自动保存传入init的超参数到checkpoint，可以从字典里的"hyper_parameters"键中找到超参

**恢复训练状态**

```python
model = LitModel()
trainer = Trainer()

# automatically restores model, epoch, step, LR schedulers, apex, etc...
trainer.fit(model, ckpt_path="some/path/to/my_checkpoint.ckpt")
```

**恢复模型权重**

Lightning 会在每个epoch结束时自动保存模型，一旦训练完成就可以按照下面的方法加载checkpoint：

```python
model = LitModel.load_from_checkpoint(path_to_saved_checkpoint)
```

下面的是手动加载的方式，与上面的方式等价：

```python
# load the ckpt
ckpt = torch.load("path/to/checkpoint.ckpt")

# equivalent to the above
model = LitModel()
model.load_state_dict(ckpt["state_dict"])
```

### 数据流

对于每一个loop（training，validation，test，predict）我们都可以实现3个hooks来自定义数据流向：

- x_step
- x_step_end(optional)
- x_epoch_end(optional)

```python
outs = []
for batch in data:
    out = training_step(batch)
    out = training_step_end(out)
    outs.append(out)
training_epoch_end(outs)
```

在Lightning中与之等价的方式为：

```python
def training_step(self, batch, batch_idx):
    prediction = ...
    return prediction


def training_epoch_end(self, outs):
    for out in outs:
        ...
```

如果使用dp/dpp2分布式模式，意味着每个batch的数据分散到了多个GPU中，有时我们可能需要将其集合起来进行处理，在这种情况下，可以实现`training_step_end()`方法来将所有devices的output进行处理来得到结果：

```python
def training_step(self, batch, batch_idx):
    x, y = batch
    y_hat = self.model(x)
    loss = F.cross_entropy(y_hat, y)
    pred = ...
    return {"loss": loss, "pred": pred}


def training_step_end(self, batch_parts):
    # predictions from each GPU
    predictions = batch_parts["pred"]
    # losses from each GPU
    losses = batch_parts["loss"]

    gpu_0_prediction = predictions[0]
    gpu_1_prediction = predictions[1]

    # do something with both outputs(average maybe)
    return (losses[0] + losses[1]) / 2


def training_epoch_end(self, training_step_outputs):
    for out in training_step_outputs:
        ...
```

整个过程的流程（伪代码）如下，lightning将如下的细节为我们隐藏：

```python
outs = []
for batch_idx, train_batch in enumerate(train_dataloader):
    batches = split_batch(train_batch)
    dp_outs = []
    for sub_batch in batches:
        # 1
        dp_out = training_step(sub_batch, batch_idx)
        dp_outs.append(dp_out)

    # 2
    out = training_step_end(dp_outs)
    outs.append(out)

# do something with the outputs for all batches
# 3
training_epoch_end(outs)
```

## 额外扩展

- [LightningDataModule](https://pytorch-lightning.readthedocs.io/en/stable/extensions/datamodules.html#datamodules)
- [Callbacks](https://pytorch-lightning.readthedocs.io/en/stable/extensions/callbacks.html#callbacks)
- [Logging](https://pytorch-lightning.readthedocs.io/en/stable/extensions/logging.html#logging)
- [Accelerators](https://pytorch-lightning.readthedocs.io/en/stable/index.html#accelerators)
- [Plugins](https://pytorch-lightning.readthedocs.io/en/stable/extensions/plugins.html#plugins)
- [Loops](https://pytorch-lightning.readthedocs.io/en/stable/extensions/loops.html#loop-customization)

## 调试

> lightning提供很多可以用来调试的工具

- 限制batches数量

  ```python
  # use only 10 train batches and three val batches per epoch
  trainer = Trainer(limit_train_batches=10, limit_val_batches=3)
  # use 20% of total train batches and 10% of total val batches per epoch
  trainer = Trainer(limit_train_batches=0.2, limit_val_batches=0.1)
  ```

  每个epoch随机选择较少数量的的batch来进行训练

- 过拟合batches

  ```python
  # Automatically overfit the same batches to your model for a sanity test
  # use only 10 train batches
  trainer = Trainer(overfit_batches=10)
  # use only 20% of total train batches
  trainer = Trainer(overfit_batches=0.2)
  ```

  每个epoch固定选择较少数量的的batch来进行训练

- 快速开发运行

  ```python
  # unit test all the code - hits every line of your code once to see if you have bugs,
  # instead of waiting hours to crash somewhere
  trainer = Trainer(fast_dev_run=True)
  
  # unit test all the code - hits every line of your code with four batches
  trainer = Trainer(fast_dev_run=4)
  ```

  对所有代码进行单元测试，看是否存在bug

- 验证检查间隔

  ```python 
  # run validation every 25% of a training epoch
  trainer = Trainer(val_check_interval=0.25)
  ```

  每1/4个epoch进行一次validation

- 性能测试

  ```python
  # Profile your code to find speed/memory bottlenecks
  Trainer(profiler="simple")
  ```

## 其他有用的特性

- [Automatic early stopping](https://pytorch-lightning.readthedocs.io/en/stable/common/early_stopping.html)
- [Automatic truncated-back-propagation-through-time](https://pytorch-lightning.readthedocs.io/en/stable/common/lightning_module.html#truncated-bptt-steps)
- [Automatically scale your batch size](https://pytorch-lightning.readthedocs.io/en/stable/advanced/training_tricks.html#batch-size-finder)
- [Automatically find learning rate](https://pytorch-lightning.readthedocs.io/en/stable/advanced/training_tricks.html#learning-rate-finder)
- [Load checkpoints directly from S3](https://pytorch-lightning.readthedocs.io/en/stable/common/checkpointing.html#checkpoint-loading)
- [Scale to massive compute clusters](https://pytorch-lightning.readthedocs.io/en/stable/clouds/cluster.html)
- [Use multiple dataloaders per train/val/test/predict loop](https://pytorch-lightning.readthedocs.io/en/stable/guides/data.html)
- [Use multiple optimizers to do reinforcement learning or even GANs](https://pytorch-lightning.readthedocs.io/en/stable/common/optimization.html#id4)