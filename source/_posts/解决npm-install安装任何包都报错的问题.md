---
title: 解决npm install安装任何包都报错的问题
date: 2019-05-13 18:21:10
tags:
  - nodejs
categories: 前端
---
在ubantu18系统上，之前不知道哪里设置错了，结果用nodejs的npm install任何一个包都报错，报错信息如下：
```
npm ERR! code ENOTFOUND
npm ERR! errno ENOTFOUND
npm ERR! network request to http://registry.cnpmjs.org/cnpm failed, reason: getaddrinfo ENOTFOUND registry.cnpmjs.org registry.cnpmjs.org:80
npm ERR! network This is a problem related to network connectivity.
npm ERR! network In most cases you are behind a proxy or have bad network settings.
npm ERR! network 
npm ERR! network If you are behind a proxy, please make sure that the
npm ERR! network 'proxy' config is set properly.  See: 'npm help config'

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/liu/.npm/_logs/2019-05-13T12_45_42_040Z-debug.log

```
按照字面意思，应该是代理出了点问题，由于对npm的设置不是很了解，没办法，百度查查别人的解决办法吧。然后就真查到了，
```
npm config set registry http://registry.cnpmjs.org
```
如果提示要sudo那就加上sudo就行了。
验证一下 ： npm info underscore 如果有返回underscore的相应信息就表示成功了。
以上内容参考网址：[http://www.cnblogs.com/nurdun/p/6824480.html](http://www.cnblogs.com/nurdun/p/6824480.html) 
