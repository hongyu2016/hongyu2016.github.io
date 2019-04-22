---
title: webpack4从头构建一个简单的vue2项目
date: 2019-04-20 16:22:45
tags: [nodejs,webpack,vue]
categories: 前端
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