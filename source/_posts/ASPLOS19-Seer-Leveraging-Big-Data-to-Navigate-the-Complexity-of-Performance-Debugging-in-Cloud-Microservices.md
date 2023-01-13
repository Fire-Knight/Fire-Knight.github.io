---
title: >-
  ASPLOS19 - Seer Leveraging Big Data to Navigate the Complexity of Performance
  Debugging in Cloud Microservices
toc: true
date: 2023-01-12 14:35:30
updated: 2023-01-12 14:35:30
tags:
- 微服务
- 智能运维
categories:
- 论文笔记
- 微服务智能运维
excerpt: 深度神经网络预测QoS违规从而实现QoS维稳
---


* 浅看了一下论文，参照中文翻译以及https://zhuanlan.zhihu.com/p/68249146
## 总结

* 主要研究领域是QoS维稳。切入方向是QoS的提取预测，以此给出伸缩扩容的建议。
* 做法：提出Seer方法，通过深度学习模型预测可能出现的QoS异常。主要通过对各种队列的分析，发现潜在的QoS异常。

  * Seer使用带有QoS违规标注并随时间收集的执行轨迹来训练一个 深度神经网络，以提示即将到来的QoS违规
* 缺点：

  * 没说明各种队列是怎么映射到神经网络中的，以及训练数据中标注的情况。
  * 队列中的数据通常为多个不同服务的请求，也没说这种关系如何在神经网络中表示

## 背景

* 系统很复杂
  * ![](image-20230112152444-ux6m8zs.png)
  * (这个图真是烂大街了，是个做微服务的都能用(狗头))

* 过去的方法检测QOS异常具有滞后性

  * ![](image-20221026145546-62s4p0c.png)
  * 50s处发现异常，采取措施，直到恢复，需要180s左右。
  * 所以希望提出一种方法，提前预测异常的发生
* 过去的通过资源使用率预测QoS是不准确的，有可能资源利用率一直高，比较难发现异常

  * ![](image-20221026150105-usoxovi.png)
  * 图二为延迟率，颜色越亮，延迟越高。从上到下为后端到前端。可以看到，后端在0s发生延时，随着时间的推移，在250s，前端感受到延时
  * 图一位资源利用率(CPU)，颜色越亮，资源利用率越高。可以看到前端一直很高，无法通过资源利用率分析QoS
## 系统架构

* ![](image-20221026150534-g91tm20.png)
## 数据来源

* ![](image-20221026151831-kxnkyh4.png)
* NIC(network interface controller)过程中的队列，TCP/IP数据传输、epoll处理、socket读、内存过程，以及应用层的队列
## 框架设计

* ![](image-20221026152354-ans09zo.png)
* 输入：队列信息、每个神经元对应一个微服务、从上到下分别为后端到前端
* 输出：每个结果代表一个微服务发送QoS异常的概率
## 实验

  * 作者比较了使用不同网络情况下的预测效果，只使用日志、使用底层队列信息等。
  * Seer has now been deployed in the Social Network cluster for over two months, and in this time it has detected 536 upcoming QoS violations (90.6% accuracy) and avoided 495 (84%) of them.