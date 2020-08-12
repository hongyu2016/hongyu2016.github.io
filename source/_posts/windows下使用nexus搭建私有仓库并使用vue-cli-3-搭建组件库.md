---
title: windows下使用nexus搭建私有仓库并使用vue-cli 3 搭建组件库
abbrlink: 73a5b140
date: 2019-08-13 10:21:42
tags:
  - nexus
  - vue
categories: 前端
---

主要分为两部分：第一部分是nexus私有仓库的搭建，第二部分是vue-cli 3的组件搭建并发布到nexus本地仓库。
## nexus私有仓库的搭建
<!-- more -->
### 环境搭建
1. 首先需要在[<font style="color:red">官网</font>](https://www.sonatype.com/oss-thank-you-win64.zip?submissionGuid=2c6a43fc-9aa5-4acc-b995-87cb63b04d66)下载nexus windows版本（注意：需要翻墙，自备梯子）。
2. 以管理员身份运行cmd（注意：必须是管理员运行且必须是cmd，win10的powershell都会报错），然后切换到`C:\node\nexus\nexus-3.18.0-01-win64\nexus-3.18.0-01\bin` 软件下载后的解压目录。
<font style="color:red">nexus启动前，最好修改下`C:\node\nexus\nexus-3.18.0-01-win64\nexus-3.18.0-01\bin\nexus.vmoptions`的配置</font>，防止出现内存不足的报错导致无法启动，我这里修改为
```
-Xms600m
-Xmx600m
-XX:MaxDirectMemorySize=1G
```
默认都是约2G

```
nexus.exe /install #执行安装命令， 成功后会提示Installed service 'nexus
nexus.exe /run #运行服务，首次运行需要等待1~2分钟
```
3. 启动完毕后,进入 http://(本机IP):8081（最好是自定义电脑ip），点击右上角Sign In进行登录，默认账号 admin
默认密码存放在`C:\node\nexus\nexus-3.18.0-01-win64\sonatype-work\nexus3\**.password` 打开文件后复制密码串进行登录，
登录后会提示修改密码，修改完重新登录即可。然后`**.password`文件就会自动删除。

### 添加npm仓库

点击左侧菜单Repositories 查看仓库列表
![](1.png)
* 点击Create repository按钮创建仓库
* group表示分组 hosted表示本机私有 proxy表示远程代理（中央仓库）
* 若registry配置为group（包括hosted和proxy）,首次会从hosted拉取，若无则从proxy拉取并缓存，下次则直接从缓存取

1. 添加npm(proxy)仓库：
选择npm(proxy)
输入`Name: npmjs.org`
`Remote storage: https://registry.npmjs.org`
如图：
![](2.png)

2. 添加npm(hosted)仓库：
选择npm(hosted)
输入`Name：npm-hosted`用于存放自己发布的私有包
如图：
![](3.png)
3. 添加npm(group)仓库：
选择npm(group)
输入`Name: npm-group`，并在Member repositories里选择之前添加的两个移到右边
如图：
![](4.png)

### 配置与验证npm仓库
添加发布角色用户及权限
1. 添加权限认证 将npm Beared Token Realm 添加至右边
如图：
![](5.png)
2. 创建nx-deploy角色并赋予一个nx-repository-view---*的权限码
![](6.png)
3. 创建deployer用户 同时设定角色为nx-deploy
![](7.png)

### 变更依赖源
安装nrm `npm i -g nrm`

```
npm config set registry https://registry.npm.taobao.org #设置为探宝源
nrm add private http://192.168.38.64:8081/repository/npm-all/ #本机ip
nrm use private #使用私有源
```
### 发布流程
每次发布前记得在package.json中检查version 有没有修改，要确认比上一个版本号高
### 编译文件
`yarn lib`或者使用npm
### 登录npm
`npm login -registry http://192.168.38.64:8081/repository/npm-hosted/`
### 发布
`npm publish -registry http://192.168.38.64:8081/repository/npm-hosted/`
发布成功后在npm-hosted 能看到发布后的包
![](8.png)
### 使用方式

```
"dependencies": {
"组件库名称": "版本号"
}

import 组件库名称 from '组件库名称'
Vue.use(组件库名称)
```
### 项目启动
`yarn serve`或者使用npm
### 组件库文件目录结构

```
├── examples                      # 示例展示  
│   ├── api                       # 接口类  
│   ├── assets                    # 资源文件夹  
│   ├── common                    # 工具类  
│   ├── components                # 项目内部组件  
│   ├── page                      # 页面  
│   ├── router                    # 路由配置  
│   ├── style                     # 页面样式问题，主题等  
│   ├── App.vue                   # 入口页面  
│   ├── main.js                   # 入口文件 加载组件 初始化等  
├── lib                           # 编译后输出的组件目录  
├── packages                      # 公共组件目录    
│   ├── query-drop-box            # demo组件  
│   ├── simple-input              # demo组件  
│   ├── index.js                  # 组件入口文件  
├── public                        # 静态资源  
│   │── favicon.ico               # favicon图标  
│   └── index.html                # html模板  
├── .gitignore                    # git 配置  
├── .npmignore                    # npm 配置  
├── vue.config.js                 # vue-cli 配置  
├── babel.config.js               # babel 配置  
├── yarn.lock                     # yarn依赖 配置  
└── package.json                  # package.json  
```
这里贴一下`vue.config.js`文件的配置：
```
const path = require('path');
function resolve(dir) {
    return path.join(__dirname, dir)
}

module.exports = {
    pages: {
        index: {
            entry: 'examples/main.js',
            template: 'public/index.html',
            filename: 'index.html'
        }
    },
    productionSourceMap: false,
    css: {
        extract: false,
        modules: false,
        sourceMap: false,
        loaderOptions: {}
    },

    // 扩展 webpack 配置，使 packages 加入编译
    chainWebpack: config => {
        config.module
            .rule('js')
            .include
            .add('/packages/')
            .end()
            .use('babel')
            .loader('babel-loader')
            .tap(options => {
                // 修改它的选项...
                return options
            })
        config.resolve.alias
            .set('@', resolve('examples'))
            .set('src', resolve('examples'))
            .set('components', resolve('examples/components'))
            .set('examples', resolve('examples'))
            .set('common', resolve('examples/common'))
            .set('packages', resolve('packages'))
            .set('api', resolve('examples/api'))
            /* 添加分析工具*/
        if (process.env.NODE_ENV === 'production') {
            if (process.env.npm_config_report) {
                config
                    .plugin('webpack-bundle-analyzer')
                    .use(require('webpack-bundle-analyzer').BundleAnalyzerPlugin)
                    .end()
                config.plugins.delete('prefetch')
            }
        }
    }
}
```