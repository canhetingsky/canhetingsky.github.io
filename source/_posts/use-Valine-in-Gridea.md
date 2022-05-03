---
title: 在Gridea中添加Valine评论系统
date: 2020-04-12 17:50:15
tags: [Gridea,Valine]
categories: 网站开发
cover: /img/use-valine-in-gridea.jpg
---

Valine - 一款快速、简洁且高效的无后端评论系统。

 Gridea 提供了两个评论系统，Gitalk 和 Disqus。Gitalk 需要使用 Github 账号登录，对于没Github 账号的人来说不太方便，Disqus 目前在国内不可用。对比了可用于静态博客的国内评论系统，最终选用了 Valine。

## Valine 特性👇

- 快速
- 安全
- Emoji 😉
- 无后端实现
- MarkDown 全语法支持
- 轻量易用(~15kb gzipped)
- 文章阅读量统计 v1.2.0+

## 快速上手 Valine

### 获取 **APPID** 和 **APPKey** 

Valine 是基于 [LeanCloud](https://www.leancloud.cn/) 开发的，所以需要注册账号来使用。首先登录或注册 [LeanCloud](https://www.leancloud.cn/), 然后进入控制台后点击左下角创建应用：

![](https://cdn.jsdelivr.net/gh/canhetingsky/media@master/img/1586595020015.jpg)

进入刚刚创建的应用，选择左下角的设置>应用 Keys，然后就能看到你的 **APPID** 和 **APPKey** 了：
![](https://cdn.jsdelivr.net/gh/canhetingsky/media@master/img/1586595106836.jpg)

### 在网页中插入 Valine

确认你是用的是哪个模板（我使用的是 Notes），然后在 Gridea theme 文件夹中找到相应的模板，并在 `head.ejs` 中引入

```javascript
<script src='//unpkg.com/valine/dist/Valine.min.js'></script>
```

![](https://cdn.jsdelivr.net/gh/canhetingsky/media@master/img/1586595765846.jpg)
在文章下面添加 Valine 的评论框，在 `post.ejs` 中插入以下代码。修改初始化对象中的 **appId** 和 **appKey** 的值为上面刚刚获取到的值即可。更多信息请查看[配置项](https://valine.js.org/configuration.html)

```html
<div id="vcomments"></div>
<script>
    new Valine({
        el: '#vcomments',
        appId: '<API_ID>',
        appKey: '<API_Key>'
    })
</script>
```

![](https://cdn.jsdelivr.net/gh/canhetingsky/media@master/img/1586596027242.jpg)
![](https://cdn.jsdelivr.net/gh/canhetingsky/media@master/img/1586596324924.jpg)
打开文章详情页，显示评论框就成功了。
![](https://cdn.jsdelivr.net/gh/canhetingsky/media@master/img/1586596974577.jpg)
评论可以在  [LeanCloud](https://www.leancloud.cn/) 的后台进行管理。
![](https://cdn.jsdelivr.net/gh/canhetingsky/media@master/img/1586596674976.jpg)
但这样管理并不理想，下一部分，使用 Valine-Admin 来对评论进行管理。

> [Valine Admin](https://github.com/DesertsP/Valine-Admin)  是 [Valine 评论系统](https://deserts.io/diy-a-comment-system/)的扩展和增强，主要实现评论邮件通知、评论管理、垃圾评论过滤等功能。支持完全自定义的邮件通知模板。

