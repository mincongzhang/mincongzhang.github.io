---
layout: post
title: Amplifier的drive,bass,mid,treble4种参数的JND实验 (附matlab无边框半透明图形代码)
category: 音频
description: 研究吉他音箱的参数, 包括JND测试以及尝试客观测量出不同参数下吉他音色“重”的程度
tags: ["音频处理"]
---

### JND(just noticeable difference)测试
图像领域的JND当属基于CIE L*a*b*的just noticeable difference实验了,貌似对音频的JND实验还没多少人做过的样子, 刚好这属于我毕设的一部分就把实验过程和结果写一下.

我的JND实验是关于吉他箱头amplifier的4种参数drive,bass,mid,treble的实验,是在REAPER软件下使用waves里的GTR模拟Marshall箱头. 
drive控制的是amplifier的增益,其值过高的时候就是过载(overdrive),在电吉他方面就会有一种失真的感觉. bass, mid, treble分别控制输入信号的低中高频.
对20个被测者(10男10女)测试后drive,bass,mid,treble的JND结果分布如下(圆形半径为每个参考量的标准差):

1.drive
    <img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_drive(1).jpg" height="200"/>
	<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_drive(2).jpg" height="200"/>


2.bass

    <img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_bass(1).jpg" height="200"/>
	<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_bass(2).jpg" height="200"/>

3.mid

    <img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_mid(1).jpg" height="200"/>
	<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_mid(2).jpg" height="200"/>


4.treble

    <img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_treble(1).jpg" height="200"/>
	<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_treble(2).jpg" height="200"/>

里面的无边框半透明圆形的代码:

```
function circle(x,y,r)
	a=0:pi/50:2*pi;
	X=x+r*cos(a); 
	Y=y+r*sin(a); 
	fill(X,Y,'b','edgealpha',0)
	alpha(0.5);
	axis equal 
end
```

5.最后论文里的分析结果,用了boxplot: 

<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_4params.jpg" height="200"/>

6.结论是:
drive方面过载(overdrive)到一定强度之后人们就分辨不出细微的区别; bass过低(低频强度不足)人们也分别不出区别,但低频增加到一定强度之后细微的调整人们都能分辨出区别;
mid增加到一定强度后人们反而分辨不出区别;
treble高频强度的从低到高人们辨识度没有太大变化. 