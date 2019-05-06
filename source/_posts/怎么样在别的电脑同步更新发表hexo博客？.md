---
title: 怎么样在别的电脑同步更新发表hexo博客？
date: 2019-04-24 22:12:23
tags: 技术
categories: 技术
---
在git上放两个分支，一个master分支，用来存放hexo 生成public代码的（发布），另一个分支随便取名字吧比如hexo，就是放整个hexo源码的，到时候在别的电脑直接克隆hexo代码，然后写文章，发布，然后同步hexo分支代码。就可以了，很简单。
<font color="red">注意：如果主题文件是从git克隆下来的，比如next主题，再提交之前先把next下的.git和.github文件夹删掉。如果之前没有删掉，发现next无法提交到github。此时要删掉你博客目录下的.git目录价下的index.lock文件</font>
然后：
```
 git rm --cached themes/next/
 git add themes/next/
 ```
 如果一次不行，尝试多几次。
 这个时候就可以提交到github了