---
title: >-
  SoCC21-VAIF-Automating instrumentation choices for performanceproblems in
  distributed applications with VAIF
toc: true
date: 2023-01-12 15:56:37
updated: 2023-01-12 15:56:37
tags:
- 微服务
- 智能运维
categories:
- 论文笔记
- 微服务智能运维
excerpt: VAIF-变化驱动的自动化插桩框架：根据trace的执行时间分布，自动开启插桩点从而帮助分析性能问题
---

## 总结

* VAIF: 变化驱动的自动化插桩框架

  * 自动根据性能问题选择需要的插桩点 -- 慢代码或不可预期的性能问题(高方差）
* 本文只需要比较时间即可 （方差大的，延时多的），不比较路径变化，每种路径变化就是一种新的trace。
* {% post_link SoCC19-Pythia-An-automated-cross-layer-instrumentation-frameworkfor-diagnosing-performance-problems-in-distributed-applications Pythia %}的改进版，只看本文即可
* 问题：只考虑了时间点的变化。

  * 方差反映不足：cache/not cache vs 一两次出问题
  * 路径出现微小变化怎么办
## 1. Intro

* 动态日志需要程序员手动指定，搜索空间大
* 自动化日志注重于错误的记录，非性能问题
* 性能问题：

  * 功能缓慢、资源争用、负载失衡
* VAIF

  * 自动插桩-在正常操作期间，VAIF的操作与今天的分布式跟踪相同，并在默认启用跟踪点级别的情况下生成跟踪。当开发人员必须诊断为什么请求很慢时，他们按下一个按钮，VAIF自动探索必须启用哪些额外的跟踪点来定位问题源。
## 2. TOWARDS AUTOMATION - 对自动化的一些解释

* 2.1 log

  * 分为：正确性log和性能问题log
* 2.2 关键点

  * 相似路径的请求会有相似的时间
  * 相似的工作流，但执行差异大(时间方差大)，则有unknow行为
  * 独立随机变量的方差可直接相加

    * 通过识别在方差中贡献最多的边 来 识别三方库中导致不确定行为的领域
* 2.3 定位规则

  * 相同的路径但是有高方差，从而锁定贡献最大的边

    * ![](image-20220106100830-wrj3clu.png)
    * a - 高方差
  * 低方差但高响应时间

    * c - 高响应时间
  * 逐步定位，逐步插桩

## 后面有时间再补