---
layout: post
title: Debug模式和Release模式下memory的区别
category: Cpp
description: Debug模式和Release模式下memory的区别
tags: ["C++"]
---

最近遇到一个诡异的bug, 在debug模式下跑的时候完全没问题, 但是在release模式下就seg fault了. 

原因可能是:
1.
debug模式下,内存分配的时候会预留一些有模式的空位. 这样就可以用来检测一些问题. 
release模式下,所有东西都包的很紧没有留空位.

2.
debug模式下,没有初始化的内存都会被初始化,比如初始化为0. 
release模式下,一般都不会初始化,都是一些乱七八糟的值. 

所以release模式下的optimization并不一定"好", 可能会把事情搞得很糟糕.


reference:
https://social.msdn.microsoft.com/Forums/en-US/9fdd11cd-ab7a-4173-8fcc-ca239b10fd20/weird-crash-in-c-mapsfind-when-compiled-in-release-mode?forum=vcgeneral

原文:
There are a few things that happen differently in release mode that can cause such things. 

For instance, the memory allocation in debug mode places unused space around each allocation filled with a pattern
(so it can be used to detect such problem). Where everything gets tightly packed without padding in Release mode.

In debug mode, memory that isn't supposed to be initialized also gets initialized with a distinctive pattern.
Sometimes that pattern can cause different code paths than the value that just coincidentally resides in the uninitialized storage in release mode
(often I find zeros in release mode rather than the cdcdcdcd, for instance).

结果竟然是要在map.find()前判断map是不是空...突然感觉C++好可怕...