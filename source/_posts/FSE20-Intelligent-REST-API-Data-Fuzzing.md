---
title: FSE20-Intelligent REST API Data Fuzzing
toc: true
date: 2023-01-12 21:25:41
updated: 2023-01-12 21:25:41
tags:
- Fuzzing
- REST API
- 测试
categories:
- 论文笔记
- REST API Fuzzing
excerpt: 通过对swagger、REST API响应的分析，智能地生成嵌入在REST API请求中的数据负载，从而发现error；微软的研究，有工具可用
---

## 要点：fuzzing，REST API，测试
## 总结

* 智能地生成嵌入在REST API请求中的数据负载
* 生成方法

* 1. schema fuzzing规则
    2. fuzzing 规则的组合方法
    3. 搜索方法（对请求body每个值fuzzing的顺序）
    4. 从swagger、响应、example中提取数据值
* 评估标准：某种fuzzing方法能出发error的类型数量，数量越多，越有效
## Intro

* > fuzzing: automatic test generation and execution with the goal of finding security vulnerabilities.
>
* 目的：评估多种fuzzing技术(对rest api请求的fuzzing)
## Background

* 这篇文章研究了如何fuzzing有状态的rest API, (尤其相对于RESTler而言）。通过智能fuzzing，生成更复杂的数据，找到更多的服务器错误
## Schema Fuzzing - 最基本的表单fuzzing

* fuzzing规则 -- 对请求body tree的fuzzing

* Node fuzzing rules

    * Drop
    * Select
    * Duplicate
    * Type
* Tree fuzzing 规则

    * single: fuzzing一个节点
    * path：选一条路径fuzzing
    * all
* 实验

* 实验评估

    * 错误信息

    * error code
    * error message
    * error type = code+message
* 实验设置

    * 实验了以上12种组合，4个node*3个tree规则
    * **每条请求有最多1000个fuzzing点** | **一个请求最多fuzzing 1000次**
* 结果

* ![](image-20211202211920-e9p52ps-20220214101124-hzqkjbl.png)
* 对于 drop和select而言，path比随机更好，随机会空间爆炸，path效率更高
* type和duplicate都可能引起反序列化错误
* 每种fuzzing都有自己发现的独有错误
* path最合适来fuzzing，不引入反序列化错误且有效率
## Combining Schema Fuzzing Rules -- fuzzing结合规则

* 基本规则

* 依次使用drop、select、duplicate、type对tree节点依次fuzzing，drop出结果后，再使用select对结果fuzzing，...
* 组合：四选二、四选三、四选四
* fuzzing次序

* BFS
* DFS
* Random
* 结论

* drop、select、type结合是很有效的，尤其是drop、select在type前fuzzing
* duplicate没什么用
* RD最有效
## Data Value Rendering - 根据数据值fuzzing

* 当前challenges：无效值会被拒绝，例如global却填入europ

* 缺少客户端信息，例如ID、group name
* 缺少domain信息，例如local、global字段或者时间
* 缺少运行时依赖信息，后一个请求依赖于前一个
* 信息来源（rendering策略）

* 静态 type-value 字典
* **从swagger提取的样例**
* **从响应中匹配字段**

    * 两种匹配策略：conservative（父节点和当前节点类型相同），aggressive（当前节点类型相同）
* render策略

* Baseline **(**BAS**)**: 字典中选择
* Examples only (EXM): example选择，然后字典选择
* Responses only (conservative) (CON):保守模式选择，然后字典选择
* Responses only (aggressive) (AGG)：aggressive选择，然后...
* Responses (conservative) and examples (CON+EXM)
* Responses (aggressive) and examples (AGG+EXM)
* 结论

* swagger样例能带来更多的错误类型覆盖
* 为叶子结点使用响应值fuzzing可以显著提高效果，尤其是激进模式
## BUG HUNTING IN CLOUD SERVICES - 对云服务器的实验

* 实验参数设置

* > Each phase has a budget of 10 times the number of nodes in the schema
    >
## 相关工作
## 讨论
