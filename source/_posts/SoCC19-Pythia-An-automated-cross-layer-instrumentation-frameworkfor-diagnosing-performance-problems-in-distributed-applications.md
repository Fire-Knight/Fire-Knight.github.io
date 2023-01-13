---
title: >-
  SoCC19-Pythia-An automated, cross-layer instrumentation frameworkfor
  diagnosing performance problems in distributed applications
toc: true
date: 2023-01-12 15:44:00
updated: 2023-01-12 15:44:00
tags:
- 微服务
- 智能运维
categories:
- 论文笔记
- 微服务智能运维
excerpt: 自动化插桩工具-监控workflow，性能变化会导致workflow的变化，从而确定插桩点
---

## 总结

* 自动化插桩工具
* 监控workflow，性能变化会导致workflow的变化，从而确定插桩点

## 1. Intro

* 目标：对新出现的性能问题，自动探索能插桩的空间，从而帮助诊断问题
## 2. Phytia

* ![](image-20220105143118-mwwxwrf.png)
* step3: 定位问题组

  * 高变异系数（协方差）或者一直都很慢

    * 一直都很慢：持续缓慢的阈值可以由它的平均响应是否落在所有请求的总体响应时间分布的尾部来确定。
* step5: 启用监控

  * 逐层启用：首先是分布式应用程序的服务，然后是服务中的组件，然后是组成组件的节点，然后是节点中的功能