---
title: webpack4从头构建一个简单的vue2项目
tags:
  - nodejs
  - webpack
  - vue
categories: 前端
abbrlink: b38ec8f3
date: 2019-04-20 16:22:45
---

### 前言
vue早就有成熟的vue-cli工具了，现在是vue-cli 3.0了，但是觉得很有必要自己去实现一下，有助于自己技术的提高，也能更好地去了解webpack。本人是webpack菜鸟，有些资料是网上学习整理过来的。

### 1.新建目录文件夹
第一步当然是新建工程目录了，具体怎么建就随便了，比如我建立webpackVue这个目录
### 2.新建package.json文件
package.json是nodejs项目必需的配置文件。使用`npm init`,按照提示一步步填写项目名称，作者等信息
### 3.webpack配置
新建build文件夹
build目录下新建webpack.base.conf.js,webpack.dev.conf.js,webpack.prod.conf.js文件，webpack.base.conf.js是公共基础配置，webpack.dev.conf.js是生产环境配置文件，webpack.prod.conf.js是生产环境配置。
创建一个src目录，用来存放我们的项目源文件，然后创建main.js文件，创建一个index.html
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>webpack4搭建vue</title>
</head>
<body>
    <div id="app"></div>
</body>
</html>
```
![](1.jpg) 目录结构是这样的

下载webpack webpack-dev-server webpack-cli (这里webpack是局部安装)
`npm i webpack webpack-dev-server webpack-cli -D`
打开webpack.base.config.js，逐步添加配置
```
var path = require('path');
var webpack = require('webpack');
module.exports ={
    //入口文件
    entry:{
        main: path.resolve(__dirname, '../src/main.js')
    },
    //输出目录
    output:{
        path: path.resolve(__dirname, '../dist'),
        filename: '[name].[hash].js'
    },
    module:{
        rules:[]
    },
    plugins:[
        new webpack.HashedModuleIdsPlugin(), // 解决vender后面的hash每次都改变
    ],
    resolve:{

    }
}
```
### 4.配置loader
配置babel编译代码：下载babel-loader，babel-core，babel-preset-dev。
`npm i babel-loader babel-core babel-preset-env -D`

babel-preset-env 帮助我们配置 babel。我们只需要告诉它我们要兼容的情况（目标运行环境），它就会自动把代码转换为兼容对应环境的代码。
babel-core是作为babel的核心存在，babel的核心api都在这个模块里面
这里有个注意的地方，就是babel的版本的问题，运行的时候根据提示安装合适的版本就好了，这里不记得是babel的哪个工具需要7的版本了。
在webpack.base.conf.js文件中增加babel配置
```
rules:[
    {
        test: /\.js$/, //匹配.js结尾的文件
        use: ['babel-loader'],
        exclude:/node_modules/ //排除node_modules里面的js
    },
]
```
新建.babelrc文件，.babelrc是babel全局配置文件
```
{
    "presets":[
        ["env",{
            "targets": {    
                "browsers": ["> 1%", "last 2 versions", "not ie <=8"],
                //"chrome": 52,
                //"browsers": ["last 2 versions", "safari 7"]
            },       
            "modules": false
        }]
    ]
}
```
#### 下载file-loader
`npm i file-loader -D`
继续追加配置
```
rules:[
    {
        test: /\.(jpg|png|svg|gif)$/,
        use:['file-loader']
    },
    {
        test:/\.(woff|woff2|eot|ttf|otf)$/
    },
]
```
#### 下载css-loader，vue-style-loader，sass-loader， node-sass，less，less-loader并配置css，scss，sass,less
`npm i css-loader vue-style-loader sass-loader node-sass less-loader less -D`
```
rules:[
    {
        test:/\.(sa|sc|c)ss$/,
        use:[
            {
                loader: 'vue-style-loader'
            },
            'css-loader',
            'sass-loader'
        ]
    },
    {
        test:/\.less$/,
        use:[
            {
                loader: 'vue-style-loader'
            },
            'css-loader',
            'sass-loader'
        ]
    },
]
```
### 5.处理html文件
下载html-webpack-plugin
`npm i html-webpack-plugin -D`
```
var HtmlWebpackPlugin = require('html-webpack-plugin'); //在base配置文件头部引入
new HtmlWebpackPlugin({
    template: path.resolve(__dirname, '../index.html')
}),
```
### 6.解析模块的配置和配置别名
```
resolve: {
    // 能够使用户在引入模块时不带扩展
    extensions: ['.js', '.json', '.vue', 'css'],
    //别名
    alias: {
        'vue$':'vue/dist/vue.esm.js',
        '@': path.resolve(__dirname, '../src')
    }
    
}
```
### 7.配置webpack.dev.conf.js文件
下载 webpack-merge，用于合并配置
`npm i webpack-merge -D`
```
var merge = require('webpack-merge');
var baseConfig = require('./webpack.base.conf');
var path = require('path');
var webpack = require('webpack');
module.exports = merge(baseConfig, {
    devtool: 'inline-source-map', // 压缩方式
    mode: 'development',
    devServer: {
        hot: true, // 热更新
        open: true, // 自动打开页面
        contentBase: path.resolve(__dirname, '../src'), // 告诉服务从哪提供内容
    },
    plugins: [
        new webpack.HotModuleReplacementPlugin(), //开启热更新
    ]
})
```
### 8.配置webpack.prod.conf.js
```
var merge = require('webpack-merge');
var baseConfig = require('./webpack.base.conf');
var path = require('path');
var webpack = require('webpack');
module.exports = merge(baseConfig, {
    devtool: 'source-map', // 压缩方式
    mode: 'production',
    plugins: [

    ]
})
```
### 9.配置vue-loader
首先要下载vue，以及vue相关的模块
`npm i vue vue-loader vue-template-compiler -D`
继续完善webpack.base.cond.js
```
var VueLoaderPlugin=require('vue-loader/lib/plugin');
//省略部分代码...
rules:[
    {
        test: /\.vue$/,
        use: ['vue-loader'],
        exclude: /node_modules/
    },
]
//省略部分代码...
plugins:[
    new VueLoaderPlugin(), // 它的职责是将你定义过的其它规则复制并应用到 .vue 文件里相应语言的块
]
```
在src目录新建app.vue文件和main.js文件
```
//app.vue
<template>
    <div>{{str}}</div>
</template>
<script>
  export default {
    name: 'App',
    data () {
      return {
        str: '欢迎来到自定义webpack4+vue构建'
      }
    }
  }
</script>
<style lang="scss">
  div {
    color: red;
  }
</style>
```
```
//main.js
import Vue from 'vue'
import App from './App'

new Vue({
    el: "#app",
    render: (h) => h(App)
})
```
### 10.配置命令

打开package.json文件，并配置开发和打包命令
```
// 省略代码。。。
"scripts": {
  "dev": "webpack-dev-server  --progress --config build/webpack.dev.conf.js",
   "build": "webpack --config build/webpack.prod.conf.js"
  }

```
`npm run dev` 就可以启动项目了
![](2.jpg)

## 继续优化...

### 11. 区分环境引入不同地址

新建config文件夹并新建dev.env.js和prod.env.js
```
//dev.env.js 开发环境配置
'use strict'

module.exports = {
    NODE_ENV: "development",
    BASE_API: "http://1456",
}
```

```
//prod.env.js 生产环境配置
'use strict'

module.exports = {
    NODE_ENV: "production",    BASE_API: "http://123.com",
}

```
### 12.优化webpack配置
解决更改文件打包时dist文件没有清除，再次打包。
下载clean-webpack-plugin，并配置webpack.prod.conf.js文件
`npm i clean-webpack-plugin -D`

```
//webpack.prod.conf.js
// 引入clean-webpack-plugin
var CleanWebpackPlugin = require('clean-webpack-plugin');
// 省略代码。。。。
plugins: [
    new CleanWebPackPlugin();
]

```
根据不同环境提取css
下载mini-css-extract-plugin，并配置webpack.prod.conf.js文件
`npm i mini-css-extract-plugin -D`
```
//webpack.prod.conf.js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
module: {
    rules: [
        {
            test: /\.(c|sc|sa)ss$/,
            use: [
                {
                loader: MiniCssExtractPlugin.loader,
                },
                'css-loader',
                'sass-loader'
            ]
        },
        {
        test: /\.less$/,
        use: [
            {
            loader: MiniCssExtractPlugin.loader,
            },
            'css-loader',
            'less-loader'
            ]
        }
    ]
},
plugins: [
    new MiniCssExtractPlugin({
        filename: '[name].[hash].css'
    })
]

```
<font color="red">webpack.base.conf.js中删除使用vue-style-loader的代码，并在webpack.dev.conf.js中定义</font>

```
module: {
    rules: [
      {
        test: /\.(c|sc|sa)ss$/,
        use: [
          {
            loader: 'vue-style-loader',
          },
          'css-loader',
          'sass-loader'
        ]
      },
      {
        test: /\.less$/,
        use: [
          {
            loader: 'vue-style-loader',
          },
          'css-loader',
          'less-loader'
         ]
      }
    ]
  },
//以上这段webpack.base.conf.js中的代码移到webpack.dev.conf.js中
```
[这个配置vue-loader中有提到](https://link.juejin.im?target=https%3A%2F%2Fvue-loader.vuejs.org%2Fzh%2Fguide%2Fextract-css.html%23webpack-4)

#### 第三方库单独打包 
把依赖的第三方库抽取出来单独打包，加快打包进度
下载autodll-webpack-plugin
`npm i autodll-webpack-plugin -D`

在webpack.base.conf.js中配置
```
//webpack.base.conf.js
//省略部分代码
var AutodllWebpackpackPlugin = require('autodll-webpack-plugin');
plugins: [
    new AutodllWebpackpackPlugin ({
        inject: true,
        debugger: true,
        filename: '[name].js',
        path: './dll',
        entry: {
            vendor: ['vue']
        }
    })
]

```
tips: inject 为 true，插件会自动把打包出来的第三方库文件插入到 HTML。filename 是打包后文件的名称。path 是打包后的路径。entry 是入口，vendor 是你指定的名称，数组内容就是要打包的第三方库的名称，不要写全路径，Webpack 会自动去 node_modules 中找到的。
#### 提取公共代码
在 webpack.base.conf.js的 plugins 属性中添加如下插件对象
`new webpack.optimize.SplitChunksPlugin()`
#### 打包时压缩js和css
下载optimize-css-assets-webpack-plugin和uglifyjs-webpack-plugin

`npm i uglifyjs-webpack-plugin uglifyjs-webpack-plugin optimize-css-assets-webpack-plugin -D`

在webpack.prod.conf.js中分别引入optimize-css-assets-webpack-plugin和uglifyjs-webpack-plugin并增加optimization
```
//webpack.prod.conf.js
var OptimizeCSSAssetsPlugin = require("optimize-css-assets-webpack-plugin");
var UglifyJsPlugin = require("uglifyjs-webpack-plugin");
// 省略部分代码
optimization: {
        minimizer: [
        // 压缩JS
        new UglifyJsPlugin({
        uglifyOptions: {
            compress: {
            warnings: false, // 去除警告
            drop_debugger: true, // 去除debugger
            drop_console: true // 去除console.log
            },
        },
        cache: true, // 开启缓存
        parallel: true, // 平行压缩
        sourceMap: false // set to true if you want JS source maps
        }),
        // 压缩css
        new OptimizeCSSAssetsPlugin({})
    ]
},

```
#### css自动加前缀
下载postcss-loader 和autoprefixer
`npm i postcss-loader autoprefixer -D`
分别在webpack.dev.conf.js和webpack.prod.conf.js的use中添加postcss-loader
在module中的rules中的use中追加：
```
use: [
    'postcss-loader'
]
```
在项目下增加postcss.config.js
```
// 配置cssz加前缀
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
```
差不多就这样了,对了，还有一点就是打包出来的html里面引用`/dll/vendor.js`需要手动改成`./dll/vendor.js`这个下次也要优化下，我记得vue-cli 2版本中有一个地方是可以配置的