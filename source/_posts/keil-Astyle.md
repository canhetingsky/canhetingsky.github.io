---
title: 在keil中使用Astyle格式化代码
tags:
  - Astyle
  - Keil
categories:
  - 嵌入式开发
  - 工具
abbrlink: d0d5c401
date: 2020-04-14 19:56:12#
---

## Astyle是什么

Astyle的全称是Artistic Style，它是一个开源的源代码格式化工具，可以对C，C++，C#以及[Java](https://lib.csdn.net/base/javase)等编程语言的源代码进行缩进、格式化、美化。
Home Page: [https://astyle.sourceforge.net/](https://astyle.sourceforge.net/)
Project Page: [https://sourceforge.net/projects/astyle/](https://sourceforge.net/projects/astyle/)

## 如何在Keil中使用Astyle神器

在Keil μVision中集成Astyle（以下Keil μVison5为例），可以将凌乱的代码变得整齐起来，方便阅读。

### Keil中配置Astyle

1. 下载Astyle，解压到任意位置（Astyle为绿色软件，无需安装）

![AStyle_2.02.1_windows.zip](https://media.canheting.cn//img/202208271516779.png)

![img](https://media.canheting.cn/img/202208271109569.png)

1. keil µVision5中单击Tools菜单---Customize Tools Menu

   ![](https://media.canheting.cn/img/202208271110267.png)

2. 添加Astyle All Files 和Astyle Current File菜单(自定义菜单名，可以随便起名)这里添加了两个菜单，分别是格式化当前文件和格式化project中的所有文件Command命令：在刚才解压的位置选择Astyle.exe。

   Arguments：

   Astyle Current File菜单填写 `!E`

   Astyle All Files菜单填写 `"$E*.c" "$E*.h"`
![img](https://media.canheting.cn//img/202208271517883.png)

![img](https://media.canheting.cn//img/202208271517752.png)![img](https://media.canheting.cn//img/202208271518100.png)

------

### Keil使用Astyle格式化代码

使用方式如下：

![img](https://media.canheting.cn//img/202208271519616.png)

使用效果很不错，代码瞬间变得整齐了。

### Astyle的进阶配置

以上配置在格式化代码时是使用的默认格式化配置。另外，Astyle支持配置代码格式化的格式。

未完待续...