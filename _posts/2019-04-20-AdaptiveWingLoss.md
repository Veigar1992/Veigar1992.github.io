---
layout: post
title: AWing-Adaptive Wing Loss for Robust Face Alignment via Heatmap Regression
category: 技术
tags: [CNN, Face Alignment]
description: ICCV2019
---

> [论文](https://arxiv.org/abs/1904.07399)，[pytorch版本源码](https://github.com/protossw512/AdaptiveWingLoss)
> 目前针对deep network的热图方式的研究很多，且精度比回归方式好，但很少有研究热图的损失函数。目前主流的热图损失函数都是均方误差（MSE），在本文中则根据Wing Loss的启发，提出了一种新的针对热图的损失函数----Adaptive Wing Loss。为了解决前景像素和背景像素之间的不平衡问题，还提出了Weighted Loss Map。为了进一步提高人脸对齐精度，还引入了boundary prediction 和 CoordConv。

# 论文贡献

1. 受Wing Loss[1]启发，提出针对热图的Adaptive Wing Loss；
2. 为了解决前景像素和背景像素之间的不平衡问题，提出了Weighted Loss Map；
3. 提出将边界boundary和CoordConv的训练方式。

# 整体结构

<p align="center">
    <img src="/assets/img/AWing/overview.png">
</p>

<p align="center">
    图1.模型整体结构示意图
</p>

本文针对的是损失函数和新的训练方式，没有模型结构的优化。结构为4 stacked HourGlass模型，输入图像256x256，输出为64x64。输入图像由GT bbox裁剪而得。

输出通多会附加一个，用来预测boundary。所以输出的热图包含C个landmark和1个boundary。

# Adaptive Wing Loss

<p align="center">
    <img src="/assets/img/AWing/diffloss.png">
</p>

<p align="center">
    图2.不同loss对比
</p>

<p align="center">
    <img src="/assets/img/AWing/wing.png">
</p>

<p align="center">
    <img src="/assets/img/AWing/awing.png">
</p>

<p align="center">
    图3.Wing Loss与AWing Loss的不同
</p>

wing loss是人脸关键点回归方式的loss，预测值的范围为0-1。wing loss在error特别大时，梯度为常数，当error比较小时，梯度比L1和MSE都要大。因此在小的error时能够放大，从而得到更好的结果。

但是Wing Loss无法克服在$y-\widehat{y}=0$出梯度不连续的问题，导致相较于LI Loss，更难收敛。这个属性导致Wing loss不适合应用在heatmap regression方法上。因为热图有很多error极小的背景像素，不成比例。实验显示，在热图上使用Wing Loss从来没有正常收敛过。

因此我们提出了Adaptive Wing Loss。

有两个值得注意的点：

1. 前景像素：训练时，一开始更加专注于减少这部分的error，然后随着误差接近于0，这部分的影响也迅速减小，不在关注这些“足够好”的像素。这样有助于训练的收敛、
2. 背景像素：可以表现的像MSE一般，即随着训练误差的减少，地图逐渐减小至0。

以上两点，单一的loss并不能同时满足，因此loss能适应GT heatmap的不同像素强度，这就是Adaptive Wing Loss提出的思路。且值得注意的是，该公式为heatmap的值为0-1的情况。

# Weighted loss map

在热图上，前景像素站的比例极少，仅有1.2%，因此假如每个像素的weight一样，就会出现不平衡，导致训练收敛缓慢和性能不佳。

<p align="center">
    <img src="/assets/img/AWing/loss_map_mask.png">
</p>

<p align="center">
    <img src="/assets/img/AWing/weight_loss.png">
</p>

<p align="center">
    图4.Weighted loss map
</p>

# Boundary 和 CoordConv

添加Boundary prediction作为未来的一个子任务。

进一步提高精度。

# 参考文献

[1] Zhen-Hua Feng, Josef Kittler, Muhammad Awais, Patrik Huber, and Xiao-Jun Wu. Wing loss for robust facial landmark localisation with convolutional neural networks. In The IEEE Conference on Computer Vision and Pattern Recognition (CVPR), June 2018

