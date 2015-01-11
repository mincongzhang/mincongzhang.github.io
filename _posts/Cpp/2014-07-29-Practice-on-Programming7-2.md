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

6.二进制文件读写

```
int x = 10;
fout.seekp(20,ios::beg);		//从beginning开始之后的20个字节的位置
fout.write((const char*)(&x),sizeof(int));

fin.seekg(0,ios::beg);
fin.read((char*)(&x),sizeof(int));

//二进制文件读写,直接写二进制数据,记事本看未必正确
```

```
/*例子1,2:读,写*/
//从键盘输入几个学生的姓名的成绩,并以二进制文件形式存起来
//从存放的二进制文件读取

#include<iostream>	//cin cout cerr之类的
#include<fstream>	//文件流
#include<cstring>

using namespace std;

class CStudent{
  public:
	char szName[20];
	int nScore;
}

int main(){
	Cstudent s;
	
	//写
	ofstream OutFile("c:\\tmp\\students.dat",ios::out|ios::binary);
	
	while(cin>>s.szName>>s.nScore){
		if(stricmp(s.szName,"exit")==0) break;
		OutFile.write((char*)&s,sizeof(s));
	}
	
	OutFile.close();
	
	//读
	ifstream inFile("students.dat",ios::in|ios::binary)
	
	if(!inFile){
		cout<<"error"<<endl;
		return 0;
	}
	while(inFile.read((char*)&s,sizeof(s))){
		int nReadedBytes = inFile.gcount();//看刚才读了多少字节
		cout<<s.szName<<" "<<s.score<<endl;
	}
	
	inFile.close();
	
	return 0;
}
```

*注意:文本文件/二进制文件打开文件的区别
(1)在Unix/Linux下,二者一致,没有区别;
(2)在Windows下,文本文件是以"\r\n"作为换行符
-读出时,系统会将0x0d0a只读入0x0a
-写入时,对于0x0a系统会自动写入0x0d
(3)所以最好读写一致(二进制写出就二进制读入)

```
/*例子3:同时读和写*/
//将students.dat文件的Jane的名字改成Mike

#include<iostream>	//cin cout cerr之类的
#include<fstream>	//文件流
#include<cstring>

using namespace std;

class CStudent{
  public:
	char szName[20];
	int nScore;
}

int main(){
	CStudent s;
	fstream iofile(""c:\\tmp\\students.dat",ios::in|ios::out|ios::binary); //注意是fstream
	if(!iofile){
		cout<<"error";
		return 0;
	}
	
	iofile.seekp(2*sizeof(s),ios::beg);//定位写指针到第三个记录(数过2个记录)
	iofile.write("Mike",strlen("Mike")+1);//???
	iofile.seekg(0,ios::beg);//定位读指针到开头
	
	while(iofile.read((char*)&s,sizeof(s))){
		cout<<s.szName<<" "<<s.nScore<<endl;
	}
	
	iofile.close();
	
	return 0;
}
```

```
/*例子4:文件拷贝*/
//用法示例:
//mycopy src.dat dest.dat
//即将src.dat拷贝到dest.dat
//如果dest.dat原来就有,则原来的文件会被覆盖

#include <iostream>
#include <fstream>

using namespace std;

int main(int argc,char * argv[]){
	if(argc!=3){
		cout<<"File name missing!"<<endl;
		return 0;
	}
	
	ifstream inFile(argv[1],ios::binary|ios::in);  //打开文件用于读
	
}
```
