---
title: ASE18 - Delta Debugging Microservice Systems
toc: true
date: 2023-01-12 21:11:18
updated: 2023-01-12 21:11:18
tags: 
- 微服务
- 智能运维
categories:
- 论文笔记
- 微服务智能运维
excerpt: 一种基于增量调试方法的故障定位方案
---

## 总结

* 提出了一种基于增量调试方法的故障定位方案
* 定位系统配置以及信息顺序带来的问题
* 特点：使用istio来进行调用和返回顺序的控制
* 感觉实际使用价值不大，现实过于复杂，增量的变量过多
## 引言

* 微服务复杂性 -- 故障调试困难

* 四个维度：节点、实例、配置和交互顺序
* 贡献

* 一个基础设施平台
* 一个增量测试算法
## 增量测试

* 通过在变化的环境下反复执行相应的测试，可以识别出与测试失败相关的故障因
素和不相关的故障因素。
* 即：找出导致测试错误的最小范围
## 过程

* 1. 找最简环境：首先确认在故障环境下应该运行失败的测试用例可以在最原始的环境（即最简环境）下运行通过
2. 找最小故障因素集合：使应该运行失败的测试用例仍然出现同样状况的失败结果，并且应该运行通过的测试用例全部通过
## 增量调试

* 维度：节点数目、实例数目、配置、交互顺序（微服务调用的执行和返回顺序）
* 目的：推导最小故障集合
* 表示：用一个向量来表示上述四个维度，每个维度含一个或多个位
* 具体过程：见《周翔-博士论文》 p63

## 本文同复旦CodeWisdem"周翔"的博士毕业论文《基于轨迹分析的微服务故障定位》第二部分，可自行参阅