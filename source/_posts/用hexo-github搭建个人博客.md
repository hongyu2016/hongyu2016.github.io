---
title: 用hexo+github搭建个人博客
tags: 技术
categories: 技术
abbrlink: 4443dac6
date: 2019-02-27 15:33:57
---
## 用hexo+github page快速搭建个人静态博客

### 1.hexo环境搭建

首先需要电脑安装好了nodejs（具体安装步骤略过），还需要有github账号。打开cmd命令行，全局安装hexo ``npm install -g hexo-cli`` 可以使用 ``hexo v`` 来检查是否安装成功。
<!-- more -->
接下来使用hexo cli来创建项目：使用``hexo init`` 就会在当前文件夹下创建了项目，当然也可以使用``hexo init 文件夹名称`` 来创建文件夹的同时创建项目。接着运行 ``npm install``安装依赖，注意，安装hexo的文件夹需要是空文件夹。

### 2.hexo的基本使用及常见的命令

``hexo s`` 使用这个命令可以在本地跑起来，可以看到cmd窗口提示项目运行在 ``http://localhost:4000``，打开浏览器输入提示的地址即可访问了，不过可以看到，默认的主题有点丑，我们可以换一个，在这里我们换next这个简洁的主题。我们可以直接百度搜索hexo next主题官网或者去hexo官网找next主题都可以，我们找到next的github，克隆下来，放到博客项目的themes目录下，接下来打开根目录的配置文件_config.yml文件找到 theme，把默认的该为next，然后命令行``hexo clean && hexo s``刷新页面即可成功替换主题了。
快速创建文章命令为 ``hexo new "文章名字"``  执行完这个命令后就会在sources下的_posts目录下创建名称为 刚刚创建的同名md文件，可以用编辑器直接打开编辑了。
#### 这里需要注意：在next主题的配置文件下添加了菜单后，点击了提示找不到页面，不要慌，是因为默认只生产home和Archive页面，需要自己创建相应的页面，比如about页面 ``hexo new page "about"`` 这时 source里面多了个目录about，里面有个index.md，就可以正常访问了，内容就在那个index.md里添加

### 与github对接

在github上新建一个名为xxx.github.io 的仓库，然后在hexo的_config.yml中的

``` 
deploy: 
  type: git 
  repository: git@github.com:hongyu2016/hongyu2016.github.io.git 
  branch: master
```
填写相应的git地址，然后需要把本地项目与git仓库相关联起来，需要使用ssh来连接，git ssh连接请参考 [配置git ssh](https://www.cnblogs.com/superGG1990/p/6844952.html) 。在本地仓库与远程仓库连接的过程中可能会出现分支不匹配等错误，需要具体问题然后再进行针对性解决。本地仓库与远程仓库关联上后就可以在本地进行发布了，``hexo d``为发布命令，会将项目发布到github page，发布成功后，会在本地生成的静态文件推送到到github，其他文件是不会推送的，这个时候会把原本在github上的readme文件删除，不要慌，在resoures下新建readme文件再次发布就好了，注意：千万不要使用github的git push发布，这样发布是没有效果的，github会在每次push时发编译失败的邮件给你，因为github page只支持静态文件，所以只能使用``hexo d``命令，另外在发布前最好先``hexo clean && hexo g``先清除和重新生成静态文件发发布。发布成功后，输入xxx.github.io就可以正常访问博客了。

## 结语：搭建hexo博客还是比较简单的，个人觉得在推送到github的过程中比较麻烦，容易出各种问题，这个地方需要注意。