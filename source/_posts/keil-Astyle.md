---
title: 在keil中使用Astyle格式化代码
date: 2020-04-14 19:56:12
tags: [Astyle, Keil]
categories: 
- 嵌入式开发
- 工具
---

​    Astyle 的全称是Artistic Style的简称，是一个开源的源代码格式化工具，可以对C，C++，C#以及[Java](https://lib.csdn.net/base/javase)等编程语言的源代码进行缩进、格式化、美化。
Home Page: [https://astyle.sourceforge.net/](https://astyle.sourceforge.net/)
Project Page: [https://sourceforge.net/projects/astyle/](https://sourceforge.net/projects/astyle/)

在Keil μVision中集成Astyle（以下Keil μVison5为例），可以将凌乱的代码变得整齐起来，方便阅读。

1. 下载Astyle，解压到任意位置（Astyle为绿色软件，无需安装）

![AStyle_2.02.1_windows.zip](https://note.youdao.com/yws/public/resource/15909f20655397a9e2beb64403f41e45/12AFB017E290410EBAFD9449EB25AF83)

![img](https://note.youdao.com/yws/public/resource/15909f20655397a9e2beb64403f41e45/BACFAE738C0D4B5EB1A950A0EA4E345C)

1. keil µVision5中单击Tools菜单---Customize Tools Menu

   ![](https://note.youdao.com/yws/public/resource/15909f20655397a9e2beb64403f41e45/9582923B342143FB8ACB3722C47C42AB)

2. 添加Astyle All Files 和Astyle Current File菜单(自定义菜单名，可以随便起名)这里添加了两个菜单，分别是格式化当前文件和格式化project中的所有文件Command命令：在刚才解压的位置选择Astyle.exe。

   Arguments：

   Astyle Current File菜单填写 `!E`

   Astyle All Files菜单填写 `"$E*.c" "$E*.h"`
![img](https://note.youdao.com/yws/public/resource/15909f20655397a9e2beb64403f41e45/10358AF584A14E4B9C56BF8C0145DA57)

![img](https://note.youdao.com/yws/public/resource/15909f20655397a9e2beb64403f41e45/32094915EF604CE18E6B68E0CDAC1F11)![img](https://note.youdao.com/yws/public/resource/15909f20655397a9e2beb64403f41e45/B3E57A456EBD48C5A974259CBA15615B)

------

使用方式如下：

![img](https://note.youdao.com/yws/public/resource/15909f20655397a9e2beb64403f41e45/5A52496B528E4CF4B49C242B8E74799F)

使用效果很不错，代码瞬间变得整齐了。