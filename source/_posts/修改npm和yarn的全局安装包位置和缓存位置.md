---
title: 修改npm和yarn的全局安装包位置和缓存位置
abbrlink: '239611e0'
date: 2021-12-17 10:21:46
tags:
  - nodejs
categories: 前端
---

最近发现C盘越来越小了，经过排查，C盘的用户文件夹很大，有30多G，实在是太大了。基本上都是应用产生的数据，其中npm目录就占了大概4、5G，实在是太大了，不能忍，于是就网上找解决方法 ，找到一个更改npm和yarn缓存目录的方法。

<!-- more -->
### npm 全局安装设置和缓存设置
查看当前npm包的全局安装路径：
`npm prefix -g `

查看配置列表：
`npm config ls `

修改npm的包的全局安装路径： 
`npm config set prefix "D:\nodejs\npm_global"`

修改npm的包的全局cache位置:
`npm config set cache "D:\nodejs\npm-cache"`

### 需要将 `D:\nodejs\npm_global` 添加进系统环境变量中，这里文添加进用户变量中。

### yarn 的安装路径和缓存路径

查看 yarn 全局安装位置：
`yarn global dir`

查看 yarn 全局cache位置:
`yarn cache dir`

改变 yarn 全局安装位置
`yarn config  set global-folder "D:\nodejs\yarn\global"`

改变 yarn 全局cache位置:
`yarn config set cache-folder`

### 注意！以上命令需要用管理员运行cmd，执行相应的命令，否则会报错，让你怀疑人生，且网上查不到相应的报错信息。而且设置完这些路径后，卸载nodejs再重装，也不会恢复默认，估计只有重装系统才能恢复默认了。
