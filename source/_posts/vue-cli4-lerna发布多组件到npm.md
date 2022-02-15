---
title: vue cli4+lerna发布多组件到npm
abbrlink: 3b9b1ab3
date: 2022-02-15 10:54:41
tags:
---

# vue cli 工具
使用vue create 创建一个应用，

# lerna
1. 全局安装`lerna`，`npm install lerna -g`，
2. 切换到刚刚创建的项目目录，执行`lerna init`，生成了packages 目录，这就是组件目录了，如图：![](1.png)
组件的package的文件内容为：![](2.png)
3. `lerna bootstrap` 执行每个包的依赖的安装
4. 根目录下的packages 文件：![](3.png)
5. 根目录下的lerna.json 文件：![](4.png)

# 编译、发布
执行 `npm run l-build` 编译组件，然后再执行`npm run l-publish`，发布道npm，这里需要注意，发布道npm前需要先提交到Git仓库