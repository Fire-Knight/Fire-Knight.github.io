---
title: ICSE19-RESTler Stateful REST API Fuzzing
toc: true
date: 2023-01-12 21:20:20
updated: 2023-01-12 21:20:20
tags:
- Fuzzing
- REST API
- 测试
categories:
- 论文笔记
- REST API Fuzzing
excerpt: 分析swagger文档推断生产者消费者关系，动态分析响应确认请求可行性，从而削减测试空间，发现error
---

* 关键词：fuzzing，swagger，响应，rest api
## 重点：有状态的rest api fuzzing

* 根据swagger推断关系（先后、生产者消费者）

* A请求返回a对象，B请求使用a对象作为参数，则A应当在B之前执行
* 根据响应动态反馈

* 了解到“请求序列a之后的请求C;请求B被服务拒绝”，在将来避免这种组合
## 摘要

* restler: 有状态的rest api fuzzing方法/工具
* 分析服务的api描述，据此fuzzing
* fuzzing依据

* 推断规范中声明的请求类型之间的生产者-消费者依赖关系
* 分析从先前测试执行期间观察到的响应的动态反馈，以便生成新的测试
## INTRODUCTION
## PROCESSING API SPECIFICATIONS
* 后面有时间再补