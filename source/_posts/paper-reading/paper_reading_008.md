# Notes on Long-Term Temporal Convolutions for Action Recognition

## 一. 论文思想

典型的人体动作经常持续数秒，只学习少数帧无法对动作在其持续时间范围内进行有效建模，将具有特定特征的视频分解为clips，并在视频层面将各个clips的信息加以聚合的方法可能不是最优的，因此该论文尝试通过长期时间卷积（LTC）来学习视频的有效表示。为了不让复杂度过高，使模型易于处理，作者在增加时间范围的同时降低了空间分辨率。

## 二. 模型结构

作者以C3D模型为基础，扩展该模型实验了不同输入时间分辨率（${20,40,60,80,100}$）和空间分辨率（${58*58; 71*71}$）的效果。作者认为如果在网络的高层保留更大的时间分辨率可以学习更为复杂的模式。该论文做了丰富的实验。

<img src="https://raw.githubusercontent.com/coelien/image-hosting/master/img/202203311545215.png" alt="image-20220331154503089" style="zoom: 50%;" />

