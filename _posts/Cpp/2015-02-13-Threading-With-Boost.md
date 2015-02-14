---
layout: post
title: 用Boost玩线程 I: 创建线程(Threading With Boost Part I: Creating Threads)
category: Cpp
description: Threading With Boost
tags: ["C++"]
---

这个网站把Boost里Threading的玩法讲解的非常好, 搬运过来翻译一下.
http://antonym.org/2009/05/threading-with-boost---part-i-creating-threads.html

## 用Boost玩线程 I: 创建线程(Threading With Boost Part I: Creating Threads)

Boost是C++里的一组便携类库(portable class libraries)[1], 功能非常强大. 它能完成诸如日期/时间操作, 
文件系统界面, 网络, 数值编程, 进程间通信以及很多其他工作.

Boost的文档非常详实, 但新手见到也容易怯步. 所以这系列文章的第一篇会从基本线程讲起, 并且附带完整的代码示例.

首先, 假设你已经知道了一般的线程概念和如何在你的平台上使用他们. 也假设你已经安装好Boost并且可以使用它了. 我们重点关注如何从最基本开始使用Boost线程 (Boost threads).

这篇文章也重点关注不同的创建线程的方法. 真正的多线程系统如同步(synchronisation )和互斥(mutual exclusion), 还需要更多技术细节,后面的文章中会介绍到. 

### 概述
'boost::thread' 对象代表了一个单个执行的线程, 就像你平常会在操作系统提供的接口上创建和管理的那样. 比如: 
在POSIX系统上, 一个Boost线程会使用 'Pthreads' API; 在Win32系统上它会使用本地的 'CreateThread' 和相应的调用. 
因为Boost抽象出所有平台内定义的代码, 你可以非常容易地写出复杂和便携的代码, 并且在所有主流平台运行. 线程对象可以被设成特殊的非线程状态, 在这种情况下, 它是处于非激活的状态(或者还没有在线程函数里运行). 

'boost::thread' 对象通常通过线程函数或者方法创建 (原文: A boost::thread object is normally constructed by passing the threading function or method it is to run). 实际上有很多不同的创建方法. 以下会讲述主流的线程建立方法. 

### 代码案例
所有代码都可以在原文地址下载, 并且可以随意使用. 下载方法和地址见原文, 这里就略过. 

### 干"实事"




reference:
[1] portable class libraries: a class library that can run on various platforms.

another tutorial:
http://blog2.emptycrate.com/content/multithreaded-c-part-1-pthreads
