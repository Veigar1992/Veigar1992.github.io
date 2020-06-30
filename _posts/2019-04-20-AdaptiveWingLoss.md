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

1. 受Wing Loss启发，提出针对热图的Adaptive Wing Loss；
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

