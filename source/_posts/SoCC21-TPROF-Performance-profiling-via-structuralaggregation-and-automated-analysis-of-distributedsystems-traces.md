---
title: >-
  SoCC21-TPROF-Performance profiling via structuralaggregation and automated
  analysis of distributedsystems traces
toc: true
date: 2023-01-12 16:19:18
updated: 2023-01-12 16:19:18
tags:
- 微服务
- 智能运维
categories:
- 论文笔记
- 微服务智能运维
excerpt: 一种基于trace的数据聚合展示方法，定位性能问题的同时合理展示数据
---

## trace分析工具
  * 我们的目标是设计新的自动化聚合方法，这些方法利用跟踪结构来支持对性能问题进行更详细的分析。
## 总结

  * 分布式系统的性能问题分析
  * 通过对trace数据的聚合和分组，来定位系统的缓慢部分，定位最有可能的性能问题
  * 提出了subspan的定义
  * 提出了多个聚合、分组的方法来定位问题
  * 不得不说论文表达有些模棱两可，导致很难理解report图的意思，猜了很久，最后转述了一下

## 1. Intro

  * 性能问题表现为slowdown
  * trace手动比较很难，用户需要知道正确的轨迹--这也是Spectroscope的缺点
  * 目标是设计新的自动化聚合方法，这些方法利用跟踪结构来支持对性能问题进行更详细的分析。
  * 定位过程

    * 从顶层到底层

      * 时延 -- 平均、尾延迟
      * 请求类型
      * trace结构：缓存命中/未命中
      * subspan
  * 设计了一个自动工具能够分析结构以及发现可能问题
  * 贡献

    * 设计了一个新的智能运维器，能够聚合trace。在结构的每一层，把trace分组，来进行聚类和分析。低的层被更详细得分组，更详细得分析
    * 提出subspan，使用subspan和span数据来进行分析
## 2. 相关工作

  * 传统的粗粒度分析器

    * 通过operation排序，损失trace结构信息

      * ![](image-20221101145254-v2znoqn.png)
    * 通过trace feature分割，损失trace结构信息

      * ![](image-20221101145337-n9xysum.png)
    * 通过path聚合，损失延迟信息

      * ![](image-20221101145853-j7fxg5t.png)
  * 累积并计算函数平均时间，不适合发现一小部分变慢（方差问题）
  * 分布式分析耗时很大（数据很多）
## 3. tprof

  * 回答两个问题

    * 哪些trace应该被分组
    * 哪些数据应该被聚合到一个组中
  * 定义四种聚类分析方法，每个方法有两个步骤

    * 通过trace结构聚类
    * 分析数据
  * 3.2 第1/2层

    * 第一层：

      * 所有trace分为一组，代表整体的行为，看哪些请求最慢，类似之前的粗粒度分析
    * 第二层

      * 按照请求类型、接口进行分组 put/get
    * 分组依据：操作/服务名聚合
    * 分析方法

      * 50th/99th, 平均延迟, 标准差等
      * 也分析 operation self时间
  * 3.3 第3层

    * 分组：trace完全相同(父子结构),但不考虑子结构的顺序。

      * 相比前两层，缓存未命中和查询数据库的跟踪将与缓存命中的跟踪分离
      * eg: A先调用B再调C和ACB是一样的
      * 可以去除并行导致调用顺序的不同
    * 数据：child_diff

      * ![](image-20220116223052-2zyjvye.png)
      * [I] 无法考虑调用之间的依赖，第二个依赖于第一个的结束
  * 3.4 第四层

    * 分组：考虑结构和顺序
    * 数据：subspan
    * 分析：同上
  * 3.6 尾延迟

    * 90%可以用于尾延迟判断
  * 3.7 诊断方法（上文分析方法的详细版）

    * 第一层

      * 排序：operation_self * 执行次数进行排序，确定最慢的操作
      * [I] 没有考虑节点见调用/网络延迟
    * 第二层

      * 分析，是什么原因（从请求类型的角度）导致了第一层中某些操作慢
      * 此外，尾分布定位 tail/normal
    * 第三层

      * 排序：对span执行时间分析
      * 此外，child_diff * 计数，确定跨度最慢部分
    * 第四层

      * 排序：平均subspan持续时间 * 计数 * fraction of time
## 4 实例 **这块很重要，全文的中心思想**

  * report如图所示

    !(image-20221101152411-sjnk3vr.png)
  * 得出report的过程

    1. 检查所有的Operation，包含所有中间span。发现post-storage-service的服务readPosts的平均自执行时间最慢。

        于是有了表格的第一层，这里相当于确定了一个要被诊断的span
    2. Request type检查，相当于检查span的所有父span(一个服务接口可能被很多span调用，父span就是一个request type)。发现ReadUserTimeline这个调用可能是尾延迟。（尾延迟是指ReadUserTimeline span在readPosts的所有父span中属于比较慢的一个)

        于是有了表格的第二层，这里相当于确定了被诊断的span的父span
    3. child检查，相当于检查被诊断span的子调用。发现存在一个subspan很慢，相当于定位根因
    4. 展示上面三层
  * 简而言之，一层确定问题span，二层确定其父调用（占问题span的所有父调用的80.3%[Q])，三层确定其子调用/问题所在(占 问题span和其问题父调用共同组成的trace 的73.3%)。

    三层共同确定一个trace结构，同时保证问题trace/span尽可能排在图表report的前面

    [Q] 此处存疑，原文为80.3% of all traces，是所有trace，还是所有带有问题span的trace？