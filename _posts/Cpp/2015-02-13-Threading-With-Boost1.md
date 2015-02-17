---
layout: post
title: 用Boost玩线程 I - 创建线程(Threading With Boost Part I - Creating Threads)
category: Cpp
description: Threading With Boost
tags: ["C++"]
---

这个网站把Boost里Threading的玩法讲解的非常好, 搬运过来翻译一下.
http://antonym.org/2009/05/threading-with-boost---part-i-creating-threads.html

-----
Boost是C++里的一组便携类库(portable class libraries)[1], 功能非常强大. 它能完成诸如日期/时间操作, 
文件系统界面, 网络, 数值编程, 进程间通信以及很多其他工作.

Boost的文档非常详实, 但新手见到也容易怯步. 所以这系列文章的第一篇会从基本线程讲起, 并且附带完整的代码示例.

首先, 假设你已经知道了一般的线程概念和如何在你的平台上使用他们. 也假设你已经安装好Boost并且可以使用它了. 我们重点关注如何从最基本开始使用Boost线程 (Boost threads).

这篇文章也重点关注不同的创建线程的方法. 真正的多线程系统如同步(synchronisation )和互斥(mutual exclusion), 还需要更多技术细节,后面的文章中会介绍到. 

-----
### 概述
-----
`boost::thread` 对象代表了一个单个执行的线程, 就像你平常会在操作系统提供的接口上创建和管理的那样. 比如: 
在POSIX系统上, 一个Boost线程会使用 'Pthreads' API; 在Win32系统上它会使用本地的 'CreateThread' 和相应的调用. 因为Boost抽象出所有平台内定义的代码, 你可以非常容易地写出复杂和便携的代码, 并且在所有主流平台运行. 线程对象可以被设成特殊的非线程状态, 在这种情况下, 它是处于非激活的状态(或者还没有在线程函数里运行). 

`boost::thread` 对象通常通过线程函数或者方法创建 (原文: A boost::thread object is normally constructed by passing the threading function or method it is to run). 实际上有很多不同的创建方法. 以下会讲述主流的线程建立方法. 

-----
### 代码案例
-----
所有代码在[原文](http://antonym.org/2009/05/threading-with-boost---part-i-creating-threads.html)都有提供下载, 并且可以随意使用. 下载方法和地址见原文, 这里就略过. 

-----
### 干"正事"
-----
在我们深入研究之前, 先对我们要干的"正事"提个醒. 在下面的一系列例子中, 我们会调用一个简单的sleep函数, 来模拟在线程中的真正工作("正事"). 这些是为了模拟一些要费时间执行的代码, 但因为和例子无关, 就用`sleep`代替. 

Sleep一段时间最简单的方法是用Boost创建一个时间段的对象, 然后把这个传递给`boost::this_thread`类. 这个类可以让我们很方便的找到正在跑的线程, 要不然可能得很麻烦地在别的不相关的函数里找来找去. 以下是实现`sleep`的方法:

```
// Three seconds of pure, hard work!
boost::posix_time::seconds workTime(3);
boost::this_thread::sleep(workTime);
```

接着在`main()`函数里,我们等着工作线程(worker)完成,然后调用`join()`将线程中执行的结果加入回主线程. 这会让主线程sleep等待,直到工作线程(worker)完成.

细心的读者会想知道我们产生这么一个线程, 仅仅是让它等一段时间完成, 有什么好处呢? 确定那个连接了执行路径再把我们稳当地放回了连续的世界(sequential world)? 创建这么一个线程的目的是什么? 好吧, 到我们搞清楚怎么用同步机制(synchronisation mechanisms)之前, 这都是解释线程创立最直接了当的方法. 还有要知道怎么`join`线程也很重要, 我们调用`workerThread.join()`等待线程完成, 并且将结果同步回主线程. 

下面的图例展示了例子的一般结构:

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/Cpp/Threading-With-Boost1_BoostThreadExample.png" height="200"/>



reference:
[1] portable class libraries: a class library that can run on various platforms.

another tutorial:
http://blog2.emptycrate.com/content/multithreaded-c-part-1-pthreads
