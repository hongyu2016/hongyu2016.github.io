---
title: 运行yarn eject 后 yarn start 报错
tags: 'react,'
categories: 前端
abbrlink: e1691e31
date: 2020-06-04 19:00:34
---
运行 yarn eject 把webpack配置暴露出来后，运行yarn start后报错，
报错的内容大致是 找不到 react-scripts下的config/env.js文件，然后查了下，发现node_module下 react-scripts被清空了，package.json文件
<!-- more -->
下也没有这个记录了，找了很多资料都没有结果，然后索性就按照这个包，按照好后就报各种插件版本低，比如webpack，babel等，然后全部升级后，启动就没有报错了。