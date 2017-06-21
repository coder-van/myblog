---
title: 代码之外
date: 2017-03-30 18:02:28
tags:
- cloud
- ai
- internet
categories: out-code
---

关于CPU和IO
---
1. 服务端程序处理事情总体概括为逻辑计算和和IO，IO可以分为内存、文件和网络。

2. 负责逻辑运算的CPU和内存的处理速度远远高于文件和网络处理速度，CPU和IO之间的速度差导致了对IO的等待，等待过程产生不同的IO处理方式问题。

3. IO处理方式并不只是阻塞非阻塞：blocking IO 阻塞｜ non-blocking IO 非阻塞| IO multiplexing 多路复用（也叫event driven IO) ｜ Asynchronous I/O 异步。深入理解看[博客](http://blog.csdn.net/historyasamirror/article/details/5778378)
