---
title: 让支付宝 微信小程序等小程序 支持scss
tags:
  - 小程序
categories: 前端
abbrlink: fbf9056
date: 2020-08-26 14:20:28
---

原生的钉钉小程序是不支持scss的，支付宝小程序编辑器也不支持配置转译scss，我们使用vscode安装scss插件的方式，来将scss文件转译为acss文件
<!-- more -->
## 安装插件 `Live Sass Compiler`
## 配置vscode

```
    {
      "liveSassCompile.settings.formats": [
        {
          "format": "expanded",
          "extensionName": ".wxss",
          "savePath": null
        }
      ],
      "liveSassCompile.settings.generateMap": false  // 去掉map文件
    }
```
## 点击vscode 底部 watch 按钮,即可监听文件变动，动态编译文件
![](1.png)
