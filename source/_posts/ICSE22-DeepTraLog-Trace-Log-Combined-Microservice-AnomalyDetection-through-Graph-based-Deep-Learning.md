---
title: >-
  ICSE22-DeepTraLog: Trace-Log Combined Microservice AnomalyDetection through
  Graph-based Deep Learning
toc: true
date: 2023-01-12 14:48:39
updated: 2023-01-12 14:48:39
tags:
- 微服务
- 智能运维
categories:
- 论文笔记
- 微服务智能运维
excerpt: 结合log和trace的混合服务异常检测方法：基于图深度学习，不需要预训练模型，线上分类检测异常。可以说是目前微服务智能运维内分析最全面效果较好的方法。
---


## 总结

* 提出log和trace的混合服务异常检测方法
* 基于图深度学习，不需要预训练模型，线上分类检测异常
* 解决两个痛点

  * 基于log的异常检测：现有的日志异常检测方法将日志视为事件序列，无法处理分布在具有复杂交互的大量服务中的微服务日志。（事件只针对于当前服务，没有上下调用关联）
  * 基于trace的异常检测：忽略了由调用层次结构和并行/异步调用带来的复杂的结构。（主要还是对并行请求的处理）
* 提出DeepTraLog: 一种基于深度学习的微服务异常检测方法。DeepTraLog使用统一的图表示来描述跟踪的复杂结构，同时结构中嵌入日志事件。结合trace和log训练GGNNs(基于deep SVDD)模型
<!-- more -->

## 摘要

* 基于log和基于trace的问题
* 介绍DeepTraLog
## Intro

* 基于trace的异常检测: 没有处理并行，以及log中的信息
* 基于log的异常检测：使用日志+时间戳为每个节点日志排序，但缺乏调用链组成信息（不认同，带traceId即可）
* 提出DeepTraLog

  * 使用一种统一的图表示，称为跟踪事件图(trace event graph, TEG)，来描述跟踪的复杂结构，以及该结构中嵌入的日志事件。
  * 它以轨迹和日志作为输入，训练基于图的深度学习模型用于轨迹异常检测
  * 过程

    * 首先，它解析输入跟踪和日志，并分别从中提span关系和日志事件。
    * 其次，它为span事件和log事件生成向量表示，同时为每个trace构造一个TEG。
    * 第三，它训练一个门控图神经网络(ggnn)基于深度SVDD(支持向量数据描述)模型，学习每个TEG的潜在表示和最小化的数据封闭超球。
    * 当用于异常检测时，DeepTraLog以类似的方式分析跟踪和相关日志，并使用训练过的模型生成跟踪的潜在表示。然后，它根据其异常分数(即从迹的潜在表示到超球的最短距离)确定迹是否异常。
  * 注意：不依赖于标签，实时训练
* 贡献：

  * 一个统一的结合trace和log的图表示
  * 一个基于deep SVDD的GGNNS模型用来异常检测
## 背景

* 日志：日志消息是一个非结构化的句子，它包含一个固定的部分。注意：有固定的结构

  * ![](image-20220518221147-u12x4sj.png)
* motivation

  * log问题（同上）
  * trace问题（同上）
## APPROACH

1. Log Parsin：log日志转化为事件

    * 日志解析，日志中附加traceID和spanID
    * 使用Drain算法(在线日志解析，讲原本结构转化为目标结构

2. Trace Parsing：trace解析为span，同时解析为同步和异步

    * 同步：clent/server
    * 异步：生产者/消费者
    * 一个span，涉及父span的调用、接收。子span的调用接收，共四个事件
3. Event Embedding：事件嵌入

    1. log日志预处理，删除动词、单复数变幻
    2. 词向量化为, 每个词生成300维的向量(已有算法)
    3. 句子向量化, 为每个词生成权重。句子被向量化为权重的和，再归一化

        ![](image-20220518222107-g17dbkh.png)

    类似的操作向量化span事件
4. Graph Construction：图构建

    * 事件关系：同步请求、同步响应、异步请求

    1. 日志事件连接。对于span的每个范围，获取属于该范围的所有日志事件。然后，根据时间戳对日志事件进行排序，并将每个日志事件的序列关系添加到与其相邻的日志事件(时间关系，非并行关系)
    2. log日志并入span event
    3. 关系处理：client的request和response的request添加同步关系

    ![](image-20220518223141-m1w9td1.png)
5. Model Training: 一分类问题

    * 深度SVDD通过包含数据潜在表示的最小化超球来学习训练数据的有用特征表示。因此，远离超球中心的数据可以被认为是反常的。
    * 因此，我们使用门控图神经网络(ggnn)学习跟踪表示，并使用深度奇异值映射(SVDD)联合训练ggnn。
    * 损失函数：最小化超球来包含TEGs的向量
6. Anomaly Detection: 异常检测

    * 异常分数定义为TEG的潜表示到被学习的超球的最短距离

![](image-20220518223334-hppcyt3.png)
