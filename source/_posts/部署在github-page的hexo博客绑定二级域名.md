---
title: 部署在github page的hexo博客绑定二级域名
tags: 技术
categories: 技术
abbrlink: a7642960
date: 2019-02-26 19:45:45
---
## 前提：已经有了域名（这里以阿里云为例）以及能成功访问的github page（这里以hexo部署的博客为例）。
### 1.增加域名解析：

![](/images/2019-2-26.png)
这里需要注意，记录类型需要选择CNAME，主机记录为除www和@外的字符，因为我要用blog作为二级域名，所以写blog。解析线路选默认就好了，一开始我选境外，结果不行。记录值为``` ***.github.io ```(即你的github page地址)。到这里解析设置完成了。

### 2.增加域名解析：

在GitHub博客仓库的根目录中新建文件CNAME （没有后缀），里面填写 ``` blog.iyuge.cn ``` 注意，因为hexo在部署至github时会重新删除github的文件，所以我们必须在本地的source目录下新建CNAME文件，然后``` hexo clean && hexo g && hexo d github ```上就有CNAME文件了

### 结语：至此，给github page绑定二级域名就完成了，很简单，但是需要注意的是第一次有可能不成功需要重试几次，每次可能需要等一段时间才会生效！