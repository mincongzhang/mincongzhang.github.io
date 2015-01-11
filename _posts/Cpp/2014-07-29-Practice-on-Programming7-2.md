---
layout: post
title: Practice on Programming笔记（第七周：文件操作）
category: Cpp
description: 文件操作
tags: ["C++"]
---

### 文件操作
1.数据的层次
(1)位   bit
(2)字节 byte
(3)域/记录:

```
//例如:学生记录
int ID;
char name[10];
int age;
int rank[10];
```

(4)将所有记录顺序地写入一个文件; 这样的文件叫做 *顺序文件*

2.文件和流(stream)
(1)顺序文件:一个有限字符构成的顺序字符流(Character Stream)
另:字节流（Byte Stream)
(2)C++标准库:ifstream,ofstream,fstream公三个类
(3)以上三个类用于文件操作-统称为*文件流类*

(4)ifstream:读取
(5)ofstream:写入
(6)fstream: 读取/写入

3.文件操作
(1)使用/创建文件的基本流程:打开文件=>读/写文件=>关闭文件
(2)(打开文件)目的:
-通过指定文件名,建立文件和文件流对象的关联;
-指明文件的使用方式
(3)(读/写文件)目的:
-利用读/写指针进行相应位置的操作

4.建立顺序文件

```
//方式1

#include <fstream>

ofstream outFile("client.dat",ios::out|ios::binary);//打开文件
/*
(1)ofstream是fstream中定义的一个类
(2)outFile是自定义的ofstream类的对象
(3)clients.dat是将要建立的文件的文件名
(4)ios::out|ios::binary是打开并建立文件的选项
- ios::out 输出到文件,删除原有内容
- ios::app 输出到文件,保留原有内容,总是在尾部添加(appendix)
- ios::binary 以二进制文件格式打开文件
*/
```

```
//方式2

//也可以先创建ofstream对象,再用open函数打开
ofstream fout;
fout.open("test.out",ios::out|ios::binary);
//判断打开是否成功
if(!fout){cerr<<"File open error"<<endl;}

//文件名可以给出绝对路径,也可以给相对路径
//没有交代路径信息,就是在当前文件夹下找文件
```

5.文件的读写指针
(1)对于输入文件,有一个读指针
(2)对于输出文件,有一个写指针
(3)对于输入输出文件,有一个读写指针
(4)表示文件操作的当前位置,该指针在哪里,读写操作就在哪里进行

```
/*写指针*/
ofstream fout("a1.out",ios::app);
long location = fout.tellp();取得写指针的位置

//还可以赋值
location = 10L;
fout.seekp(location);			//将写指针移动到第10个字节处
fout.seekp(location,ios::beg);	//从头数location
fout.seekp(location,ios::cur);	//从当前位置数location
fout.seekp(location,ios::end);	//从尾部数location(location可以为负)
```

```
/*读指针*/
ifstream fin("a1.in",ios::in);
long location = fin.tellg();取得读指针的位置

location = 10L;
fin.seekg(location);			//将读指针移动到第10个字节处
fin.seekg(location,ios::beg);	//从头数location
fin.seekg(location,ios::cur);	//从当前位置数location
fin.seekg(location,ios::end);	//从尾部数location(location可以为负)
```