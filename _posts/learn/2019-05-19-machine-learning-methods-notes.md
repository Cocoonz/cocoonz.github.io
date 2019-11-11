---
layout: post
title: 赵志勇《Python机器学习算法》笔记（更新中）
category: 学习
tags: machine&learning, python
description: 
---

## 0 绪论

### 分类

- **监督学习（Supervised Learning）**：训练数据包含了类别信息，典型问题是分类（Classification）和回归（Regression），典型算法有Logistic Regression、BP神经网络和线性回归算法。
- **无监督学习（Unsupervised Learning）**：训练数据中不包含类别信息，典型的问题是聚类（Clustering），代表算法为K-Means、DBSCAN算法等。
- 半监督学习（Semi-Supervised Learning）：训练数据中有一部分数据包含类别信息，另一部分不包含类别信息，是监督学习和无监督学习的融合。其算法一般是在监督学习的算法上进行扩展，使之可以对未标注数据建模。
- 增强学习（Reinforcement Learning）：强调如何基于环境而行动，以取得最大化的预期利益。如Alpha Go在人机对战中通过对当前棋局的分析决定下一步的落子。

#### 监督学习

分类和回归算法的最主要的区别是，分类算法中的类别是离散的值，如广告点击问题中的类别是 {+1, -1}，分别表示点击和未点击；而回归算法中的类别是连续的值，如通过人的身高、体重、性别信息预测人的年龄（年龄是连续的正整数）。

# 第四部分 推荐算法

## 14 协同过滤（Collaborative Filtering, CF）算法



#### 