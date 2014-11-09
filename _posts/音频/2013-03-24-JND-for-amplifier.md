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
<a style="float:left;">
    <img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_drive(1).jpg" width="300"/>
</a>
    <a style="float:left;">
	<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_drive(2).jpg" width="300"/>
</a>

2.bass
<a style="float:left;">
    <img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_bass(1).jpg" width="300"/>
</a>
    <a style="float:left;">
	<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_bass(2).jpg" width="300"/>
</a>

3.mid
<a style="float:left;">
    <img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_mid(1).jpg" width="300"/>
</a>
    <a style="float:left;">
	<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_mid(2).jpg" width="300"/>
</a>

4.treble
<a style="float:left;">
    <img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_treble(1).jpg" width="300"/>
</a>
    <a style="float:left;">
	<img src="https://github.com/mincongzhang/mincongzhang.github.io/raw/master/_posts/音频/JND_treble(2).jpg" width="300"/>
</a>