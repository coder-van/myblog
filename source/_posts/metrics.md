---
title: metrics 数据监控系统之数据
date: 2017-03-28 10:39:01
tags:
- metrics
- monitor
categories: out-code
---
整个数据监控系统由 数据采集，数据聚合，数据缓存与存储，数据可视化和报警等几个模块构成。对于数据监控已经有很多优秀的开源软件，如Graphite,Grafana，StatsD等。但在寻求一种部署简单且轻量级的解决方案的过程中，发现还是很多地方很不如人意，比如Graphite部署复杂，文档不够丰富等，于是将一些开源代码进行了整理，简化原本复杂难懂或冗余的代码，用简单易懂的代码实现数据监控需求。

了解数据监控,一定要先理解其统计数据的结构。统计数据分以下几种：

- gauge   刻度
- counter 技术器
- histogram 矩形图
- timer 计时器

[go-metrics](https://github.com/rcrowley/go-metrics)项目是一个非常优秀的metrics实现方案，用Go语言实现，Grafana的metrics就是直接使用了此项目的很多代码。
