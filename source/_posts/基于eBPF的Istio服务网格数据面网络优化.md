---
title: 基于eBPF的Istio服务网格数据面网络优化
toc: true
date: 2023-01-10 21:14:21
updated: 2023-01-10 21:14:21
tags:
- 微服务
- Istio
- eBPF
categories:
- 会议纪要
- CCF2021-云原生软件技术与工程实践论坛
excerpt: CCF2021-云原生软件技术与工程实践论坛中"基于eBPF的Istio服务网格数据面网络优化"，报告方为中山大学
---

## 作者

* 杨婉琪等 中山大学
## 背景

* ![](image-20211225141642-gttvbav.png)
* ![](image-20211225141849-mzkosba.png)

* ![](image-20211225141916-2ddykm0.png)
## 优化方法

* ![](image-20211225142037-wfvypsf.png)
* 时延分布

* ![](image-20211225142117-h0vpheo.png)
* ![](image-20211225142144-fads1ne.png)
* 优化架构

* ![](image-20211225142258-azvnnht.png)

    * 简而言之，把k8s pod到代理的流量部分给优化掉，直接重定向
