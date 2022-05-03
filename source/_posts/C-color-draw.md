---
title: 使用EasyX制作一个颜色画板
date: 2020-04-14 19:47:10
tags: [C++,EasyX,编程,图形库]
categories: 编程
---

EasyX 是针对C++的图形库，可以帮助C语言初学者快速上手图形和游戏编程。  

比如，可以用 VC + EasyX 很快的用几何图形画一个房子，或者一辆移动的小车，可以编写俄罗斯方块、贪吃蛇、黑白棋等小游戏，可以练习图形学的各种算法，等等。总之，这是一个很强大的图形库。  

**通过此库设计了一个颜色画板！**

> 代码如下：  

```c++
// ***********************************************************************
// Assembly         :
// Author           : qin
// Created          : 07-18-2016
//
// Last Modified By : qin
// Last Modified On : 08-14-2016
// ***********************************************************************
// <copyright file="demo.cpp" company="">
//     Copyright (c) . All rights reserved.
// </copyright>
// <summary></summary>
// ***********************************************************************
#include<graphics.h>
#include <iostream>

using namespace std;

void box();//函数声明
int fill();//函数声明

/// <summary>
/// Mains this instance.
/// </summary>
/// <returns>int.</returns>
int main(void)
{
	initgraph(640, 480);
	box();
	fill();
	closegraph();
	return 0;
}

/// <summary>
/// 调用画格子和调色板函数.
/// </summary>
void box()//画格子及调色板函数
{
	int color[9] = { BLACK, BLUE, GREEN, CYAN, RED, MAGENTA, BROWN, YELLOW, WHITE };//调色板拥有的颜色，可以自己增加
	setlinecolor(LIGHTGRAY);//设置格子边框颜色
	for (int i = 0; i <= 16; i++)//画格子
	{
		line(i * 30 + 80, 40, i * 30 + 80, 280);//画17条竖线
		if (i <= 8)
			line(80, i * 30 + 40, 560, i * 30 + 40);//画9条横线
	}
	for (int i = 0; i <= 9; i++)//画调色板的格子
	{
		line(i * 50 + 95, 350, i * 50 + 95, 400);//画10条竖线
		if (i < 2)
		{
			line(95, i * 50 + 350, 545, i * 50 + 350);//画2条横线
		}
	}
	for (int i = 0; i < 9; i++)//填充调色板格子
	{
		setfillcolor(color[i]);//设置填充颜色
		floodfill(i * 50 + 100, 375, LIGHTGRAY);//填充颜色
	}
}
/// <summary>
/// 鼠标控制填色函数.
/// </summary>
/// <returns>int.</returns>
int fill()
{
	MOUSEMSG m;
	int whichcolor = BLACK;//颜色值,默认黑色
	while (true)
	{
		m = GetMouseMsg();
		if (m.uMsg == WM_LBUTTONDOWN)//判断左键是否按下
		{
			if (m.y >= 350 && m.y <= 400 && m.x >= 145 && m.x <=545)//判断鼠标是否位于调色板区域
			{
				whichcolor = getpixel(m.x, m.y);//返回该点的颜色
			}
			if (m.y >= 40 && m.y <= 280 && m.x >= 110 && m.x <= 560)//判断鼠标是否位于待填色格子区域
			{
				setfillcolor(whichcolor);//设置填充颜色
				floodfill(m.x, m.y, LIGHTGRAY);//填充颜色
			}
		}
		if (m.uMsg == WM_RBUTTONDOWN)//判断是否按下右键
		{
			TCHAR s[] = _T("即将退出,请稍后......");
			settextcolor(LIGHTBLUE);
			outtextxy(320 - textwidth(s)/2 , 315 - textheight(s)/2, s);
			Sleep(1000);
			for (int j = 0; j < 600; j++)
			{
				outtextxy(j, 100, s);
				Sleep(20);
			}
			return 0;//结束函数
		}			
	}
}
```
