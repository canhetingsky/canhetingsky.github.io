---
title: Valine-Admin后台搭建
date: 2020-04-14 18:33:41
tags: [Valine,Valine-Admin,网站开发]
categories: 网站开发
cover: /img/Valine-Admin.jpg
---
> [Valine Admin](https://github.com/DesertsP/Valine-Admin) 是 Valine 评论系统的扩展和增强，主要实现评论邮件通知、评论管理、垃圾评论过滤等功能。
<!-- more -->
[Valine Admin](https://github.com/DesertsP/Valine-Admin) 支持完全自定义的邮件通知模板。基于 Akismet API 实现准确的垃圾评论过滤。此外，使用云函数等技术解决了免费版云引擎休眠问题，支持云引擎自动唤醒，漏发邮件自动补发。兼容云淡风轻及Deserts维护的多版本Valine。
## 快速部署

- 在 [Leancloud](https://leancloud.cn/dashboard/#/apps) 云引擎设置界面，填写代码库并保存：<https://github.com/DesertsP/Valine-Admin.git>
![设置仓库](https://media.canheting.cn/img/1586763962331.jpg)
- 在设置页面，设置环境变量以及 Web 二级域名。
![环境变量](https://media.canheting.cn/img/1586764335298.jpg)

| 变量             | 示例                                      | 说明                                                         |
| ---------------- | ----------------------------------------- | ------------------------------------------------------------ |
| SITE_NAME        | Deserts                                   | [必填]博客名称                                               |
| SITE_URL         | [https://deserts.io](https://deserts.io/) | [必填]首页地址                                               |
| **SMTP_SERVICE** | QQ                                        | [新版支持]邮件服务提供商，支持 QQ、163、126、Gmail 以及 [更多](https://nodemailer.com/smtp/well-known/#supported-services) |
| SMTP_USER        | [xxxxxx@qq.com](mailto:xxxxxx@qq.com)     | [必填]SMTP登录用户                                           |
| SMTP_PASS        | ccxxxxxxxxch                              | [必填]SMTP登录密码（QQ邮箱需要获取独立密码）                 |
| SENDER_NAME      | Deserts                                   | [必填]发件人                                                 |
| SENDER_EMAIL     | [xxxxxx@qq.com](mailto:xxxxxx@qq.com)     | [必填]发件邮箱                                               |
| ADMIN_URL        | <https://xxx.leanapp.cn/>                 | [建议]Web主机-二级域名，用于自动唤醒                          |
| BLOGGER_EMAIL    | [xxxxx@gmail.com](mailto:xxxxx@gmail.com) | [可选]博主通知收件地址，默认使用SENDER_EMAIL                 |
| AKISMET_KEY      | xxxxxxxxxxxx                              | [可选]Akismet Key 用于垃圾评论检测，设为MANUAL_REVIEW开启人工审核，留空不使用反垃圾 |

**以上必填参数请务必正确设置。**

- 二级域名用于评论后台管理，如[https://deserts.leanapp.cn](https://deserts.leanapp.cn/) 。
![二级域名](https://media.canheting.cn/img/1586764344136.jpg)

- 切换到部署标签页，分支使用master，点击部署即可
![一键部署](https://media.canheting.cn/img/1586764501029.jpg)

- 第一次部署需要花点时间。
![部署过程](https://media.canheting.cn/img/1586764523793.jpg)

- 评论管理。访问设置的二级域名`https://二级域名.leanapp.cn/sign-up`，注册管理员登录信息，如：<https://deserts.leanapp.cn/sign-up>
 ![管理员注册](https://media.canheting.cn/img/1586764674233.jpg)

> 注：使用原版Valine如果遇到注册页面不显示直接跳转至登录页的情况，请手动删除_User表中的全部数据。

此后，可以通过`https://二级域名.leanapp.cn/`管理评论。

## 定时任务设置

目前实现了两种云函数定时任务：
- 自动唤醒，定时访问Web APP二级域名防止云引擎休眠；
- 每天定时检查24小时内漏发的邮件通知。

进入云引擎-定时任务中，创建定时器，创建两个定时任务。

选择self-wake云函数，Cron表达式为`0 0/30 7-23 * * ?`，表示每天早6点到晚23点每隔30分钟访问云引擎，`ADMIN_URL`环境变量务必设置正确：
![](https://media.canheting.cn/img/1586764761081.jpg)

选择resend-mails云函数，Cron表达式为`0 0 8 * * ?`，表示每天早8点检查过去24小时内漏发的通知邮件并补发：
![通知检查](https://media.canheting.cn/img/1586764769756.jpg)

**添加定时器后记得点击启动方可生效。**

**至此，Valine Admin 已经可以正常工作，更多[可选的进阶配置](https://github.com/DesertsP/Valine-Admin#至此valine-admin-已经可以正常工作更多以下是可选的进阶配置)查看原文介绍。**