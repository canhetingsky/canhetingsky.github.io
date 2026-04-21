---
title: 一键升级npm依赖包到最新版本教程
date: 2023-05-30 23:32:37
tags: [npm, 教程, 前端]
categories: [技术]
abbrlink: f1fdc5c5
---

有时候我们需要将项目中的 npm 依赖包升级到最新版本，一个一个手动检查太麻烦了，这时候可以使用 npm-check-updates 工具来一键升级。

<!-- more -->

## 1. 全局安装npm-check-updates

```shell
npm install -g npm-check-updates
```

## 2. 检查版本

在 package.json 所在目录（根目录）执行 `ncu`，可以查看当前的依赖版本和最新的依赖版本：

```shell
ncu
```

输出示例：

```
Checking E:\GitHub\blog\package.json
[====================] 21/21 100%

 hexo                  ^6.3.0  →   ^7.1.1
 hexo-renderer-marked  ^6.0.0  →   ^6.2.0
 hexo-renderer-stylus  ^2.0.1  →   ^3.0.1
 hexo-theme-butterfly  ^4.7.0  →  ^4.13.0
 hexo-theme-landscape  ^0.0.3  →   ^1.0.0

Run ncu -u to upgrade package.json
```

执行完毕之后，可以看到所有依赖的当前的版本和最新版本号。也可以看到提示 `Run ncu -u to upgrade package.json`。

## 3. 更新项目 package.json 文件

执行升级命令 `ncu -u`：

```shell
ncu -u
```

输出示例：

```
Upgrading E:\GitHub\blog\package.json
[====================] 21/21 100%

 hexo                  ^6.3.0  →   ^7.1.1
 hexo-renderer-marked  ^6.0.0  →   ^6.2.0
 hexo-renderer-stylus  ^2.0.1  →   ^3.0.1
 hexo-theme-butterfly  ^4.7.0  →  ^4.13.0
√ Linked 5 public hoist packages to E:\GitHub\blog\node_modules
......
```

以上操作完毕之后，查看 package.json 文件，可以看到依赖包已经更新。

## 4. 重新安装

```shell
npm install
```

## 参考

[npm-check-updates](https://www.npmjs.com/package/npm-check-updates)