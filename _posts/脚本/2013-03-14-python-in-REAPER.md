---
layout: post
title: 如何在REAPER软件上使用python脚本
category: 脚本
description: 在REAPER软件上写python脚本控制
tags: ["python"]
---

### 配环境

1.首先下载任何一个版本的REAPER,再下载python3.1以上的版本,并且配置环境(e.g.高级系统设置→环境变量→PATH→C:\python31).
其实python3.1和2.x的区别不是想象的那么大,目前发现的区别就在于()的使用,以及raw_input()这个函数的删除,见以下:

    1)Python2.5中函数调用不需要使用(), 在Python3.1中则必须使用():
      如Python 2.5中可以用：print 'Hello world!'
      在Python 3.1中必须改用：print ('Hello world!')
    2)Python2.5中输入函数可以用raw_input()
      在Python3.1中则只能用input()，删除了raw_input()函数

2.打开REAPER

3.选择 Actions 菜单, show actions list

image: reaper_script1

4.在搜索栏输入"script" 

5.选择并运行"ReaScript: open ReaScript help (html)...". 一个网页会弹出, 第二行会说是否检测到Python的安装.
(不成功的话检查PATH里的环境变量, 安装python并且配置环境变量后最好重启一下; 还有一个可能是在option里的perference没有勾选python操作)

image: reaper_script2
image: reaper_script3

6.Run the ReaScript: "Run Python script"

image: reaper_script4

7.Choose a scripted python file

image: reaper_script5

8.测试成功

image: reaper_script6

现在就可以给REAPER写各种python脚本了.

### 使用

1.API
其实弄懂REAPER的API操作起来还是不算麻烦的,虽然一开始会遇到各种奇葩问题...比如一开始想测试"helloworld!",print这些是行不通的,要用REAPER自带的  RPR_ShowConsoleMsg("helloworld!") 才能在控制台输出. 而且每个API都得加"RPR_". 最奇葩的是REAPER wiki上自带的稀有无比的示例有些还是错的,总之得自己琢磨.

如果实在找不到自己想用的API, 一个方法是用自己的想象力创造 (比如REAPER就是不能用脚本控制自动render音频的),另一点是去搜索REAPER的command编号, 它完全没把相应的名字写出来, 很多action都得自己各种试各种猜,比如

```
	RPR_Main_OnCommand(41823,0) #add project to render queue, using the most recent render settings
	RPR_Main_OnCommand(41207,0) #render all queued renders
```

我写的一个例子:

```
def render_and_rename(renderpath,newpath,rendername,newname):
   RPR_Main_OnCommand(41823,0) #add project to render queue, using the most recent render settings
   RPR_Main_OnCommand(41207,0) #render all queued renders
 if new not in os.listdir(newpat):
    os.renames(renderpath + os.sep + rendername,newpath + os.sep + newname)
 if new in os.listdir(newpat):
    RPR_ShowConsoleMsg(new+" is generated in "+newpat+"\n\n") 
```

而且python脚本是能对任何VST plugins进行操作,不限于它自己的plugins,所以WAVES上的GTR啥的都毫无压力.

2.GUI
很遗憾我到现在都没能在REAPER里写出GUI, 但理论上它是支持GUI的, 虽然在苛刻的环境变量下各种好用的python GUI比如wxPython都不能用, 但是最基本的tkinter还是支持的 (python3.1下确实是tkinter而不是Tkinter). 我没写出来的原因是每次运行GUI都会有个R6034 runtime error (不是百度google上搜出的各种Visual Studio的 r6034啊那些解决方法我都试过了全都没用,msdn上的也不是解决REAPER的这个问题而是vs的解决方法)
但是据官网论坛上的人还是做出来了不少GUI的, 有些是在Mac上做的...感觉特别牛逼的一个GUI是可以远程TCP连接操作REAPER.

Reference:
[1] http://wiki.cockos.com/wiki/index.php/ReaScript
[2] http://wiki.cockos.com/wiki/index.php/Quick_Start_ReaPython
[3] http://forum.cockos.com/forumdisplay.php?f=20
(论坛上各种问题都能问, 但是可能没人帮你解决..还是自己琢磨吧..还有,里面有很多人上传的自己的程序, 有一个资料库可以搜索下载的)
[4] http://sebug.net/paper/python/index.html (python简明教程)