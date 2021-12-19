# 行为识别survey

## github论文代码

- [ECO: Efficient Convolutional Network for Online Video Understanding](https://github.com/mzolfaghari/ECO-efficient-video-understanding)
- [TSM: Temporal Shift Module for Efficient Video Understanding](https://github.com/mit-han-lab/temporal-shift-module)

- [Temporal Action Detection with Structured Segment Networks](https://github.com/yjxiong/action-detection)
- [Online Real-time Multiple Spatiotemporal Action Localisation and Prediction](https://github.com/gurkirt/realtime-action-detection)
- [Non-local Neural Networks for Video Classification]()

## summary

- no single approach to all problems
- 行为识别传统方法依赖于目标检测，姿态检测，密集轨迹或是结构化信息
- 卷积神经网络可以提取每个帧的特征来获取视频级别的预测，它的缺点是不能有效地捕获运动信息
- 结合光流方法可以捕获短期运动信息
- 除了RGB和光流，其他形式的信息如音频，姿势，轨迹也应该加以利用
- 王等人关注于带有外观特征的密集轨迹描述符
- choutas等人编码了人体关节点的运动，得到的结果热图在时间上聚集
- 我们可以使用双流法，通过融合运动和外观信息构建时空表示，对于短时视频它比较有效，但不适于获取长期的时间动态信息
- 双流法包含了两个单独的子网络，一个用于原始图像，另一个用于堆叠的光流，通过融合两个流的softmax分数可以获取时空信息
- RNN，特别是LSTM，因其在长期时间建模的能力，在顺序任务上取得了令人瞩目的成就，所以一个可选的方案是采用LSTM来建模帧级别特征的动态信息
- Donahue等人设计了一个循环卷积架构，which cascaded a CNN with a recurrent model into a unified model。它可以在时间和空间维度学习视频级别的表示
- Ng等人将时间特征池化架构与LSTM相结合
- 王等人利用了深层3D卷积网络来处理salient-aware clips
- yuan等人合成了运动轨迹，光流，和视频分割并形成了空间-光流数据，之后使用双流法分别处理合成数据和RGB数据
- 除了2D卷积网络，3D卷积网络被提出用来处理视频。但是这些方法通常有冗余参数，并且需要提前在大规模视频数据集上预训练
- 并且，近期架构使用了注意力机制来获取视频中的显著部分，它可以克服LSTM中无法区分视频的不同部分的缺点


## no standard benchmark

- high spatial correlation among the videos makes the actual diversity in the training much lesser.
- given the similar theme (sports) across both the datasets, generalization of benchmarked architectures to other tasks remained a problem
- using pre-trained 2D CNNs as a starting point for drastically better convergence

## actions recognition approaches overview

1. Local high-dimensional visual features that describe a region of the video are extracted either densely [[3](https://blog.qure.ai/notes/deep-learning-for-videos-action-recognition-review#3)] or at a sparse set of interest points[[4](https://blog.qure.ai/notes/deep-learning-for-videos-action-recognition-review#4) , [5](https://blog.qure.ai/notes/deep-learning-for-videos-action-recognition-review#5)].(提取高维视觉特征)
2. The extracted features get combined into a fixed-sized video level description. One popular variant to the step is to bag of visual words (derived using hierarchical or k-means clustering) for encoding features at video-level.
3. A classifier, like SVM or RF, is trained on bag of visual words for final prediction

### Approach 1: Single Stream Network

1. The learnt spatiotemporal features didn’t capture motion features
2. The dataset being less diverse, learning such detailed features was tough

### Approach 2: Two Stream Networks

