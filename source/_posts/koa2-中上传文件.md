---
title: koa2 中上传文件
abbrlink: 9a00eab2
date: 2021-01-22 09:36:15
tags:
  - nodejs
  - koa2
categories: 后端
---

koa2 上传文件

本来是使用 `koa-body` 这个包来接收上传文件的，这个包也可以读取get请求，本来以为直接用它来接收文件及其他请求，把`koa-bodyparser`去掉，但是后来发现`koa-body` 接收post请求时报415，不支持的媒体类型错误，然后一时也 找不出原因，就干脆不用这个包了，接收正常的请求还是使用`koa-bodyparser`，然后文件上传接收用`@koa/multer`。


`@koa/multer`不是一个中间件，因此不能像其他中间件一样直接在app.js中引入，使用的方式如下：

```
// 前端路由
const router = require('koa-router')();
const path = require('path')
router.prefix('/api'); //路由前缀
const multer = require('@koa/multer');

// 上传配置
const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, path.join(__dirname, '../../public/upload/'));
  },
  filename: function (req, file, cb) {
    cb(null, file.fieldname + '-' + Date.now() + '.png')
  }
})

const upload = multer({ storage: storage })
// 使用multer上传
router.post('/tv/upload_channel_poster', upload.single('file'), Tv.updateChannelPoster)

```