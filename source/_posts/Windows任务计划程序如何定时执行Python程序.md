---
title: Windows任务计划程序如何定时执行Python程序
tags:
  - python
  - Windows
  - 任务计划程序
categories:
  - 教程
abbrlink: b133e156
date: 2023-04-02 14:36:35
---

# 引言

在 Windows 电脑上如果要实现定时执行 Python 程序，可以借助 Windows 任务计划程序。它是 Windows 操作系统自带的一个功能，可以让您在指定的时间或事件触发时自动执行某些操作。例如，您可以使用任务计划程序来定时执行 Python 脚本，或者在系统启动时自动打开某个程序。任务计划程序可以让您更加方便地管理您的计算机，并且可以帮助您提高工作效率。

# 使用 Windows 任务计划程序

首先在 Windows 任务栏搜索`任务计划程序`,并打开。

![](https://media.canheting.cn//img/202304021349463.png)

![](https://media.canheting.cn//img/202304021349672.png)

## 添加一个新的定时计划任务

![](https://media.canheting.cn//img/202304021350960.png)

## 输入任务名称及描述

名称必填，描述可以选择是否填写。这里名称填写`阿里云盘签到`。
![](https://media.canheting.cn//img/202304021350829.png)

## 设置任务触发周期

可以选择每天、每周、每月等。

![](https://media.canheting.cn//img/202304021350890.png)

点击下一步，具体设置触发时间，如下图设置，从 2023.4.2 号开始，晚上 8 点执行，也就是每天晚上 8 点开始执行这个任务计划。

![](https://media.canheting.cn//img/202304021350566.png)

## 设置任务要执行的动作

选择启动程序。
![](https://media.canheting.cn//img/202304021350764.png)
点击下一步，启动程序需要填写以下参数：

- 程序或者脚本(必填)：指的是任务需要执行的程序或者脚本。
- 添加参数(可选)：指的是上面的程序或者脚本要添加的参数。
- 起始于(可选)： 指的是上面的程序或者脚本工作的工作目录。

### 方式一：调用 python 解释器

- 程序或者脚本(必填)：这里填写 Python 编译器的路径，比如`D:\Python\Python310\python.exe`
- 添加参数(可选)：定时执行 Python 程序，需要在这里填写打算执行的 Python 程序的完整路径，比如：`F:\Desktop\aliyunpan_sign\aliyunpan_sign.py`
- 起始于(可选)： 这个可以不填，这里填写程序或者脚本所在文件夹即可，比如：`F:\Desktop\aliyunpan_sign`

![](https://media.canheting.cn//img/202304021350041.png)

### 方式二：调用 bat 命令

首先新建一个文本文档，并修改文件后缀名为 bat，比如`aliyunpan_sign.bat`。打开文件，在里面填写以下内容：

```python
python “F:\Desktop\aliyunpan_sign\aliyunpan_sign.py”
```

然后任务启动的参数列表参考下面内容进行天下：

- 程序或者脚本(必填)：这里填写 bat 文件的路径，比如`F:\Desktop\aliyunpan_sign\aliyunpan_sign.bat`
- 添加参数(可选)：这里不用填写
- 起始于(可选)： 这个可以不填，这里填写程序或者脚本所在文件夹即可，比如：`F:\Desktop\aliyunpan_sign`

![](https://media.canheting.cn//img/202304021350182.png)

## 确认任务并完成添加

![](https://media.canheting.cn//img/202304021350577.png)
这样在设置的时间 Python 程序就会自动执行。

> 但是请注意需要电脑开机才会执行。如果电脑开机后时间晚于任务执行时间会立即运行；如果开机时间早于任务执行时间，则会等待设置时间到了才会执行 Python 程序。

## 确认任务并完成添加

![](https://media.canheting.cn//img/202304021351572.png)
这样在设置的时间 Python 程序就会自动执行。

> 但是请注意需要电脑开机才会执行。如果电脑开机后时间晚于任务执行时间会立即运行；如果开机时间早于任务执行时间，则会等待设置时间到了才会执行 Python 程序。

## 测试任务参数是否添加正确

在任务计划列表中找到刚才添加的任务，鼠标右键，运行一次，查看 Python 脚本程序是否可以正确运行。
![](https://media.canheting.cn//img/202304021351325.png)
