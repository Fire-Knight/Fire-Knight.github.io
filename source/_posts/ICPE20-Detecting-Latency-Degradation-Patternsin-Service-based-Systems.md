---
title: ICPE20-Detecting Latency Degradation Patternsin Service-based Systems
toc: true
date: 2023-01-12 20:19:34
updated: 2023-01-12 20:19:34
tags:
- 微服务
- 智能运维
categories:
- 论文笔记
- 微服务智能运维
excerpt: 延迟请求聚类
---

## 总结

* 目标：检测异常延时
* 提出自动化检测与请求延时相关的RPC请求时间 -- **延迟退化模式**

* 本质上是延迟请求聚类
* 动态规划+遗传算法
* 但实际上，只需要关注某个请求变慢即可。或者用一个聚类算法一下处理getProfile和gerCart，所以<u>本文没什么用感觉</u>

## 1. Intro

* 在rpc执行时间中，这些复杂的非确定性行为会产生特定的模式，在本文中我们将其称为**延迟退化模式**

* 延迟降低模式的一个粗略的例子是“当getProfile执行时间超过x，而getCart执行时间超过y时，主页请求变慢”