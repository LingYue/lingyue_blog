---
title: Tornado的HTTP Server的服务模型
date: 2016-05-09 01:14:31
tags: Tornado源码分析
categories: Tornado系列
toc: true
comments: true
---


简单介绍web server的一般架构，重点介绍Tornado的HTTP Server架构模型。
<!-- more -->

首先了解一下，网络中的数据是怎么流动的。

![](Tornado的HTTP-Server的服务模型/tornado_000.gif)

