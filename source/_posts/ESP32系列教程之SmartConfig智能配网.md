---
title: ESP32系列教程之SmartConfig智能配网
tags:
  - ESP32
  - SmartConfig
categories:
  - 嵌入式开发
abbrlink: 5da28d89
date: 2022-10-22 23:42:52
---

# **SmartConfig介绍**

随着越来越多智能家居设备进入家庭，这些产品大部分都是要连接家庭的WiFi网络的。而WiFi网络的接入需要知道无线路由器的名称（SSID）和密码，绝大多数的智能家居是没有输入接口的，在设备中预先设置WiFi的名称和密码显然是不现实的，这样对于智能家居要连接的无线路由器输入无线路由器的名字和密码就成了一个困难。为了克服这个问题，人们使用了多种配网方法，比如智能家居热点配网，手机热点配网，蓝牙辅助配网等等，这些配网方式都存在一定的不方便之处，而smartConfig配网方式是这些无线配网方式里面比较方便和快捷的配网方式之一。
ESP8266、ESP32使用了ESP-Touch协议，它适用于TI开发的SmartConfig技术。SmartConfig又名快连，用于将基于Wi-Fi的新型物联网设备连接到Wi-Fi网络。当前设备在没有和其他设备建立任何实际性通信链接的状态下，一键配置该设备接入WIFI。
smartconfig的配网基本原理是通过手机直接发送报文到待配网设备。手机发送UDP广播报文，待配网设备扫描所有的可用无线信道，找到发送smartConfig的报文，并锁定在这一信道上开始接收数据。
![](https://media.canheting.cn/img/202210230003927.png)
smartconfig完成配网主要分以下3个步骤：

1. 设备进入初始化状态，开始监听附近的WiFi数据包。
2. 手机/平板设置WiFi名称和密码后，发送UDP广播包。
3. 设备通过UDP包（长度）获取配置信息，切换网络模式，连接上家里WiFi，配置完成。

# SmartConfig智能配网代码说明

1. 引入头文件

```cpp
#include <WiFi.h>
```

2. SmartConfig智能配网

SmartConfig智能配网用到的主要函数为`WiFi.beginSmartConfig()`。通过查询SmartConfig连接状态判断WiFi是否连接成功，用到的主要函数为`WiFi.smartConfigDone()`。

```cpp
void smart_config(void)
{
    // Init WiFi as Station, start SmartConfig
    WiFi.mode(WIFI_AP_STA);
    WiFi.beginSmartConfig();

    // Wait for SmartConfig packet from mobile
    Serial.println("Waiting for SmartConfig.");
    while (!WiFi.smartConfigDone())
    {
        delay(500);
        Serial.print(".");
    }

    Serial.println("");
    Serial.println("SmartConfig received.");

    // Wait for WiFi to connect to AP
    Serial.println("Waiting for WiFi");
    while (WiFi.status() != WL_CONNECTED)
    {
        delay(500);
        Serial.print(".");
    }

    WiFi.setAutoConnect(true); // 设置自动连接
}
```

3. 开机自动连接WiFi

```cpp
bool connect_wifi(void)
{
    WiFi.mode(WIFI_STA);
    WiFi.begin(); //启动WIFI连接
    Serial.println("Connection WIFI");

    int retry_count = 0;
    while (retry_count < MAX_RETRY)
    {
        delay(500);
        Serial.print(".");
        retry_count++;
        if (WiFi.status() == WL_CONNECTED) //检查连接状态
        {
            return true;
        }
    }
    return false;
}
```

4. 开机判断联网方式，初次联网，则进入SmartConfig智能配网

```cpp
void setup_wifi(void)
{
    if (!connect_wifi())
    {
        smart_config();
    }
}
```

程序运行结果

```cpp
Connection WIFI
..........Waiting for SmartConfig.
...............................................
SmartConfig received.
Waiting for WiFi

WiFi connected: Ohyes
IP address: 192.168.3.94
hello world!
hello world!
```

```cpp
Connection WIFI
.........
WiFi connected: Ohyes
IP address: 192.168.3.94
hello world!
hello world!
hello world!
hello world!
hello world!
hello world!
```

# 附录：完整代码

完整代码可在GitHub下载：[canhetingsky/ESP32_DEV/ESP32_SmartConfig](https://github.com/canhetingsky/ESP32_DEV/tree/master/ESP32_SmartConfig)

```cpp
#include <WiFi.h>

#define MAX_RETRY 10

void smart_config(void)
{
    // Init WiFi as Station, start SmartConfig
    WiFi.mode(WIFI_AP_STA);
    WiFi.beginSmartConfig();

    // Wait for SmartConfig packet from mobile
    Serial.println("Waiting for SmartConfig.");
    while (!WiFi.smartConfigDone())
    {
        delay(500);
        Serial.print(".");
    }

    Serial.println("");
    Serial.println("SmartConfig received.");

    // Wait for WiFi to connect to AP
    Serial.println("Waiting for WiFi");
    while (WiFi.status() != WL_CONNECTED)
    {
        delay(500);
        Serial.print(".");
    }

    WiFi.setAutoConnect(true); // 设置自动连接
}

bool connect_wifi(void)
{
    WiFi.mode(WIFI_STA);
    WiFi.begin(); //启动WIFI连接
    Serial.println("Connection WIFI");

    int retry_count = 0;
    while (retry_count < MAX_RETRY)
    {
        delay(500);
        Serial.print(".");
        retry_count++;
        if (WiFi.status() == WL_CONNECTED) //检查连接状态
        {
            return true;
        }
    }
    return false;
}

void setup_wifi(void)
{
    if (!connect_wifi())
    {
        smart_config();
    }

    Serial.println("");
    Serial.print("WiFi connected: ");
    Serial.println(WiFi.SSID());
    Serial.print("IP address: ");
    Serial.println(WiFi.localIP());
}

void setup()
{
    // put your setup code here, to run once:
    Serial.begin(115200);
    setup_wifi();
}

void loop()
{
    // put your main code here, to run repeatedly:
    Serial.println("hello world!");
    delay(1000);
}

```

