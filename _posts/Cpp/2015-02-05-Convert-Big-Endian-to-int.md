---
layout: post
title: Big Endian转成int方法
category: Cpp
description: Big Endian转成int方法
tags: ["C++"]
---

### Big Endian
In a big-endian system, the most significant value in the sequence is stored at the lowest storage address (i.e., first). In a little-endian system, the least significant value in the sequence is stored first. For example, consider the number 1025 (2 to the tenth power plus one) stored in a 4-byte integer:
00000000 00000000 00000100 00000001

Address	Big-Endian  	Little-Endian 
00      00000000        00000000
01	    00000100        00000001
02      00000001        00000100
03	    00000000        00000000


### 将Big Endian的二进制数据转化成uint32_t
用位移(shift):
  int32_t sequence_number = (buffer[0] << 24) | 
                            (buffer[1] << 16) | 
                            (buffer[2] << 8)  | 
                            (buffer[3]);