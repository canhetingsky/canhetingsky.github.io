---
title: ESP32系列教程之空中升级OTA
abbrlink: 3ede243a
date: 2022-10-23 00:04:49
tags:
  - ESP32
  - OTA
categories:
  - 嵌入式开发

---

# 准备工作

- 配置好开发环境，本文是基于`VSCODE + PlatformIO`
- 一个可用于联网的WiFi
- 编译一个.bin固件升级程序，并上传得到一个http网址。

首先编写一个程序，每隔1s打印`hello world!`，并生成.bin升级固件。关于如何编译生成.bin升级固件，详见附录。

```cpp
#include <Arduino.h>

void setup()
{
    // put your setup code here, to run once:
    Serial.begin(115200);
}

void loop()
{
    // put your main code here, to run repeatedly:
    Serial.println("hello world!");
    delay(1000);
}

```

# 空中升级**OTA**代码说明

程序流程讲解：开机-->等待联网-->成功联网-->升级。

1. 引入头文件

```cpp
#include <WiFi.h>
#include <HTTPUpdate.h>
```

2. 根据实际添加相关变量：wifi的名称及密码、远程升级固件的地址

```cpp
/**********根据实际修改**********/
const char *wifi_ssid = "your wifi ssid";        // WIFI名称，区分大小写，不要写错
const char *wifi_password = "your wifi password "; // WIFI密码
//远程固件链接，只支持http
const char *ota_url = "http://media-1251347578.cos.ap-beijing.myqcloud.com/firmware.bin";
/**********根据实际修改**********/
```

3. 连接wifi

```cpp
WiFi.begin(wifi_ssid, wifi_password); //连接wifi
while (WiFi.status() != WL_CONNECTED)
{ //等待连接wifi
    delay(500);
    Serial.print(".");
}
```

4. 远程固件升级

```cpp
/**
 * @brief 固件升级函数，通过http请求获取远程固件，实现升级
 *
 * @param update_url 待升级远程固件bin文件的地址
 * @return t_httpUpdate_return 升级最终状态
 */
t_httpUpdate_return updateBin(const char *update_url)
{
    WiFiClient UpdateClient;

    t_httpUpdate_return ret = httpUpdate.update(UpdateClient, update_url);

    return ret;
}
```

5. 【可选】升级过程回调函数，用于提示升级过程（失败、成功、升级进度等）

```cpp
//当升级开始时，打印日志
void update_started()
{
    Serial.println("CALLBACK:  HTTP update process started");
}

//当升级结束时，打印日志
void update_finished()
{
    Serial.println("CALLBACK:  HTTP update process finished");
}

//当升级中，打印日志
void update_progress(int cur, int total)
{
    Serial.printf("CALLBACK:  HTTP update process at %d of %d bytes[%.1f%%]...\n", cur, total, cur * 100.0 / total);
}

//当升级失败时，打印日志
void update_error(int err)
{
    Serial.printf("CALLBACK:  HTTP update fatal error code %d\n", err);
}

/**
 * @brief 固件升级函数，通过http请求获取远程固件，实现升级
 *
 * @param update_url 待升级远程固件bin文件的地址
 * @return t_httpUpdate_return 升级最终状态
 */
t_httpUpdate_return updateBin(const char *update_url)
{
    Serial.println("start update");
    WiFiClient UpdateClient;

    httpUpdate.onStart(update_started);     //当升级开始时
    httpUpdate.onEnd(update_finished);      //当升级结束时
    httpUpdate.onProgress(update_progress); //当升级中
    httpUpdate.onError(update_error);       //当升级失败时

    t_httpUpdate_return ret = httpUpdate.update(UpdateClient, update_url);

    return ret;
}
```

运行程序后，ESP32成功连接wifi即开始远程固件升级。

```
Connection WIFI..........
start update
CALLBACK:  HTTP update process started
CALLBACK:  HTTP update process at 0 of 232976 bytes[0.0%]...
CALLBACK:  HTTP update process at 0 of 232976 bytes[0.0%]...
CALLBACK:  HTTP update process at 4096 of 232976 bytes[1.8%]...
CALLBACK:  HTTP update process at 8192 of 232976 bytes[3.5%]...
CALLBACK:  HTTP update process at 12288 of 232976 bytes[5.3%]...
CALLBACK:  HTTP update process at 16384 of 232976 bytes[7.0%]...
CALLBACK:  HTTP update process at 20480 of 232976 bytes[8.8%]...
CALLBACK:  HTTP update process at 24576 of 232976 bytes[10.5%]...
CALLBACK:  HTTP update process at 28672 of 232976 bytes[12.3%]...
CALLBACK:  HTTP update process at 32768 of 232976 bytes[14.1%]...
CALLBACK:  HTTP update process at 36864 of 232976 bytes[15.8%]...
CALLBACK:  HTTP update process at 40960 of 232976 bytes[17.6%]...
CALLBACK:  HTTP update process at 45056 of 232976 bytes[19.3%]...
CALLBACK:  HTTP update process at 49152 of 232976 bytes[21.1%]...
CALLBACK:  HTTP update process at 53248 of 232976 bytes[22.9%]...
CALLBACK:  HTTP update process at 57344 of 232976 bytes[24.6%]...
CALLBACK:  HTTP update process at 61440 of 232976 bytes[26.4%]...
CALLBACK:  HTTP update process at 65536 of 232976 bytes[28.1%]...
CALLBACK:  HTTP update process at 69632 of 232976 bytes[29.9%]...
CALLBACK:  HTTP update process at 73728 of 232976 bytes[31.6%]...
CALLBACK:  HTTP update process at 77824 of 232976 bytes[33.4%]...
CALLBACK:  HTTP update process at 81920 of 232976 bytes[35.2%]...
CALLBACK:  HTTP update process at 86016 of 232976 bytes[36.9%]...
CALLBACK:  HTTP update process at 90112 of 232976 bytes[38.7%]...
CALLBACK:  HTTP update process at 94208 of 232976 bytes[40.4%]...
CALLBACK:  HTTP update process at 98304 of 232976 bytes[42.2%]...
CALLBACK:  HTTP update process at 102400 of 232976 bytes[44.0%]...
CALLBACK:  HTTP update process at 106496 of 232976 bytes[45.7%]...
CALLBACK:  HTTP update process at 110592 of 232976 bytes[47.5%]...
CALLBACK:  HTTP update process at 114688 of 232976 bytes[49.2%]...
CALLBACK:  HTTP update process at 118784 of 232976 bytes[51.0%]...
CALLBACK:  HTTP update process at 122880 of 232976 bytes[52.7%]...
CALLBACK:  HTTP update process at 126976 of 232976 bytes[54.5%]...
CALLBACK:  HTTP update process at 131072 of 232976 bytes[56.3%]...
CALLBACK:  HTTP update process at 135168 of 232976 bytes[58.0%]...
CALLBACK:  HTTP update process at 139264 of 232976 bytes[59.8%]...
CALLBACK:  HTTP update process at 143360 of 232976 bytes[61.5%]...
CALLBACK:  HTTP update process at 147456 of 232976 bytes[63.3%]...
CALLBACK:  HTTP update process at 151552 of 232976 bytes[65.1%]...
CALLBACK:  HTTP update process at 155648 of 232976 bytes[66.8%]...
CALLBACK:  HTTP update process at 159744 of 232976 bytes[68.6%]...
CALLBACK:  HTTP update process at 163840 of 232976 bytes[70.3%]...
CALLBACK:  HTTP update process at 167936 of 232976 bytes[72.1%]...
CALLBACK:  HTTP update process at 172032 of 232976 bytes[73.8%]...
CALLBACK:  HTTP update process at 176128 of 232976 bytes[75.6%]...
CALLBACK:  HTTP update process at 180224 of 232976 bytes[77.4%]...
CALLBACK:  HTTP update process at 184320 of 232976 bytes[79.1%]...
CALLBACK:  HTTP update process at 188416 of 232976 bytes[80.9%]...
CALLBACK:  HTTP update process at 192512 of 232976 bytes[82.6%]...
CALLBACK:  HTTP update process at 196608 of 232976 bytes[84.4%]...
CALLBACK:  HTTP update process at 200704 of 232976 bytes[86.1%].
..
CALLBACK:  HTTP update process at 204800 of 232976 bytes[87.9%]...
CALLBACK:  HTTP update process at 208896 of 232976 bytes[89.7%]...
CALLBACK:  HTTP update process at 212992 of 232976 bytes[91.4%]...
CALLBACK:  HTTP update process at 217088 of 232976 bytes[93.2%]...
CALLBACK:  HTTP update process at 221184 of 232976 bytes[94.9%]...
CALLBACK:  HTTP update process at 225280 of 232976 bytes[96.7%]...
CALLBACK:  HTTP update process at 229376 of 232976 bytes[98.5%]...
CALLBACK:  HTTP update process at 232976 of 232976 bytes[100.0%]...
CALLBACK:  HTTP update process at 232976 of 232976 bytes[100.0%]...
CALLBACK:  HTTP update process finished
```

升级完毕会运行升级的程序

```
hello world!
hello world!
hello world!
hello world!
hello world!
hello world!
hello world!
hello world!
```

# 注意事项

## 1. 远程固件链接需为http协议

使用OTA升级，远程固件链接不支持https协议。如果使用了https，将升级失败。
例如，将远程固件链接更换为https协议

```cpp
//远程固件链接
const char *ota_url = "https://media-1251347578.cos.ap-beijing.myqcloud.com/firmware.bin";
```

ESP运行时，进行OTA升级，会返回400失败。

```
Connection WIFI..........
start update
[  6077][E][HTTPUpdate.cpp:380] handleUpdate(): HTTP Code is (400)

[update] Update failed.
```

## 2. 远程链接确保为ESP32编译后固件

如果远程固件地址为非正常编译后bin文件，OTA升级会有校验，升级失败。

```
Connection WIFI.....
start update
CALLBACK:  HTTP update process started
[  4281][E][HTTPUpdate.cpp:323] handleUpdate(): Magic header does not start with 0xE9

[update] Update failed.
```

从中串口打印的提示信息可以看出，OTA是校验远程链接文件开头是不是0xE9。使用winhex打开编译后的固件文件，确实可以看到开始是0xE9。
![image.png](https://media.canheting.cn/img/202210230019849.png)

# 附录1 ESP32空中升级OTA完整代码

完整代码可在GitHub下载：[canhetingsky/ESP32_DEV/ESP32_OTA](https://github.com/canhetingsky/ESP32_DEV/tree/master/ESP32_OTA)

```cpp
#include <WiFi.h>
#include <HTTPUpdate.h>

/**********根据实际修改**********/
const char *wifi_ssid = "your wifi ssid";        // WIFI名称，区分大小写，不要写错
const char *wifi_password = "your wifi password "; // WIFI密码
//远程固件链接，只支持http
const char *ota_url = "http://media-1251347578.cos.ap-beijing.myqcloud.com/firmware.bin";
/**********根据实际修改**********/

//当升级开始时，打印日志
void update_started()
{
    Serial.println("CALLBACK:  HTTP update process started");
}

//当升级结束时，打印日志
void update_finished()
{
    Serial.println("CALLBACK:  HTTP update process finished");
}

//当升级中，打印日志
void update_progress(int cur, int total)
{
    Serial.printf("CALLBACK:  HTTP update process at %d of %d bytes[%.1f%%]...\n", cur, total, cur * 100.0 / total);
}

//当升级失败时，打印日志
void update_error(int err)
{
    Serial.printf("CALLBACK:  HTTP update fatal error code %d\n", err);
}

/**
* @brief 固件升级函数，通过http请求获取远程固件，实现升级
*
* @param update_url 待升级远程固件bin文件的地址
* @return t_httpUpdate_return 升级最终状态
*/
t_httpUpdate_return updateBin(const char *update_url)
{
    Serial.println("start update");
    WiFiClient UpdateClient;

    httpUpdate.onStart(update_started);     //当升级开始时
    httpUpdate.onEnd(update_finished);      //当升级结束时
    httpUpdate.onProgress(update_progress); //当升级中
    httpUpdate.onError(update_error);       //当升级失败时

    t_httpUpdate_return ret = httpUpdate.update(UpdateClient, update_url);

    return ret;
}

void setup()
{
    Serial.begin(115200); //波特率115200
    Serial.print("Connection WIFI");
    WiFi.begin(wifi_ssid, wifi_password); //连接wifi
    while (WiFi.status() != WL_CONNECTED)
        { //等待连接wifi
            delay(500);
            Serial.print(".");
        }
    Serial.println("");
    t_httpUpdate_return ret = updateBin(ota_url); //开始升级
    switch (ret)
        {
            case HTTP_UPDATE_FAILED: //当升级失败
                Serial.println("[update] Update failed.");
                break;
            case HTTP_UPDATE_NO_UPDATES: //当无升级
                Serial.println("[update] Update no Update.");
                break;
            case HTTP_UPDATE_OK: //当升级成功
                Serial.println("[update] Update ok.");
                break;
        }
}

void loop()
{
}

```

# 附录2 如何编译生成.bin升级固件

## PlatformIO编译生成.bin固件

在项目文件夹下的`.pio\build\esp32dev`，可以找到编译后固件`firmware.bin`**。**
![image.png](https://media.canheting.cn/img/202210230019970.png)

## Arduino IDE编译生成.bin固件

使用Arduino IDE开发ESP32，需要配置好开发环境。 
在Arduino IDE中，依次点击「项目」-「导出已编译的二进制文件」，编译完毕，即可在项目文件夹下看到生成的的`.bin`文件。
![image.png](https://media.canheting.cn/img/202210230018553.png)
