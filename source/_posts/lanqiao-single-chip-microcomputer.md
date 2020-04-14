---
title: 蓝桥杯单片机设计与开发
tags: [学习 蓝桥杯单片机]
typora-root-url: ..
date: 2020-04-14 19:51:48
index_img:
banner_img:
---

### 蓝桥杯单片机设计与开发实验例程

| 常用模块 | LED        | 按键              | 数码管       | DS18B20                                      | 定时器       |
| -------- | ---------- | ----------------- | ------------ | -------------------------------------------- | ------------ |
| 备注     | 亮状态控制 | 独立按键/矩阵按键 | 动态显示数据 | 根据提供的驱动，编写初始化及读取温度函数即可 | 辅助，但必会 |

<!-- more -->

> 1.LED闪烁实验

- 实验效果：LED以0.2S为间隔闪烁

```c
#include "reg52.h" 

void delay(void)	//延时函数
{
	unsigned char i,j,k;
	for(i=0; i<20; i++)
    {
        for(j=0; j<20; j++)
        {
            for(k=0; k<248; k++);
        }
    } 
}

void main(void)
{
    while(1)
    {
        P2 = ((P2&0x1f)|0x80);
    	P0 = 0xff;  //LED熄灭
    	P2 &= 0x1f;
        delay();
        
        P2 = ((P2&0x1f)|0x80);
    	P0 = 0x00;  //LED点亮
    	P2 &= 0x1f;
        delay();
    }
}
```

> 2.按键控制（独立按键）

- 实验效果：按下不同的按键，LED有不同的现象

```c
#include "reg52.h"  //定义51单片机特殊功能寄存器

/** 独立按键接口 */
sbit S7  = P3^0;
sbit S6  = P3^1;
sbit S5  = P3^2;
sbit S4  = P3^3;

//主函数
void main(void)
{    
  while(1)
  {
      if(S7 == 0)
      {
          P2 = ((P2&0x1f)|0x80);
          P0 = 0xff;  //关闭所有LED
          P2 &= 0x1f;
      }
    
      if(S6 == 0)
      {
          P2 = ((P2&0x1f)|0x80);
          P0 = 0x00;  //打开所有LED
          P2 &= 0x1f;
      }
    
      if(S5 == 0)
      {
          P2 = ((P2&0x1f)|0xa0);
          P0 &= ~(0x01<<4);  //Close
          P2 &= 0x1f;
      }
    
      if(S4 == 0)
      {
          P2 = ((P2&0x1f)|0xa0);
          P0 |= (0x01<<4); //Open
          P2 &= 0x1f;
      }
  }
}
```

> 3.数码管控装置

实验效果：所有的数码管显示的相同的数据

```c
#include "reg52.h"  //定义51单片机特殊功能寄存器
                     
code unsigned char tab[] ={0xc0,0xf9,0xa4,0xb0,0x99,0x92,0x82,0xf8,0x80,0x90};

void delay(void)	//延时函数
{
    unsigned char i,j,k;
    for(i=0; i<20; i++)
    {
        for(j=0; j<20; j++)
        {
            for(k=0; k<248; k++);
        }
    } 
}

void main(void)
{ 
	unsigned char i;
    while(1)
    {
        for(i=0;i<10;i++)
        {
            //段码：确定是哪一位数码管亮
          	P2=(P2&0x1f|0xc0);
            P0=0xff;
            P2=0x1f;
          
          	//位码：确定数码管显示的内容
            P2=(P2&0x1f|0xe0);
            P0=tab[i];
            P2=0x1f;
            delay();
        }
    }
}
```

> 4.定时器的使用

配置定时器0，工作方式是1，并开启中断；

- 定时器模式配置

```c
TMOD |= 0x01;  //配置定时器工作模式
TH0 = (65536-2000)/256;
TL0 = (65536-2000)%256; //定时器初始值  
EA = 1; //开启总中断，只有开启了总中断，其他中断才有效
ET0 = 1;  //打开定时器0中断
TR0 = 1;  //启动定时器0，即定时器0开始计数
```

- 中断服务函数

```c
void isr_timer_0(void)  interrupt 1  //默认中断优先级 1
{
    TH0 = (65536-2000)/256;
    TL0 = (65536-2000)%256;  //定时器重载 
  
    //put your code here
}
```

- 例程之基于定时器的数码管时钟

```c
#include <reg51.h>

/***************数码管段码    0    1    2    3    4    5    6    7    8    9   灭   - */
unsigned char code tab[] = {0xc0,0xf9,0xa4,0xb0,0x99,0x92,0x82,0xf8,0x80,0x90,0xff,0xbf};

unsigned char dspbuf[8]= {10,10,10,10,10,10,10,10};	//数码管显示缓冲区
unsigned char dspcom=0;	//数码管动态刷新时位置

unsigned char flag_2ms=0;	//数码管刷新标志位

unsigned int second=30,minute=15,hour=17;		//初始化时间

void display();

//主函数
void main()
{
    //定时器0初始化
    TMOD |= 0x01;
    TH0 = (65536-2000)/256;
    TL0 = (65536-2000)/256;
    EA=1;	//开启总中断
    ET0=1;	//开启定时器0中断
    TR0=1;	//启动定时器0

    while(1)
    {
        if(flag_2ms == 1)
        {
            display();
            flag_2ms=0;
        }

        //更新显示数据
        dspbuf[0] = hour/10;
        dspbuf[1] = hour%10;

        dspbuf[2] = 11;

        dspbuf[3] = minute/10;
        dspbuf[4] = minute%10;

        dspbuf[5] = 11;

        dspbuf[6] = second/10;
        dspbuf[7] = second%10;
    }
}

//定时器0中断服务函数
void timer0_int() interrupt 1
{
	//刚开始数码管时间无法更新，保持初始值；
	//原因：char最大是255，根本不会达到500，当然不会更新时间了，嘤嘤嘤！
//    static unsigned char counter=0;	
	static unsigned int counter=0;
	
    //定时器重载
    TH0=(65536-2000)/256;
    TL0=(65536-2000)/256;

    //2ms执行一次
    counter++;
    flag_2ms=1;
    if(counter==500)	//ls时间
    {
        counter=0;
        second++;
		if(second==60)
		{
			second=0;
			minute++;
			if(minute==60)
			{
				minute=0;
				hour++;
				if(hour==24)
				{
					hour=0;
				}
			}			
		}
		
    }	
}

//数码管动态显示函数
void display()
{
    //用于消除数码管闪烁
    P2=(P2&0x1f|0xe0);
    P0=tab[10];
    P2=0x1f;

    //数码管位码
    P2 = (P2 & 0x1f) | 0xc0;
    P0 = (0x01<<dspcom);
    P2 = P2 & 0x1f;

    //数码管段码
    P2 = (P2 & 0x1f) | 0xe0;
    P0 = tab[dspbuf[dspcom]];
    P2 = P2 & 0x1f;

    dspcom++;
    if(dspcom == 8)
    {
        dspcom=0;
    }
}
```

