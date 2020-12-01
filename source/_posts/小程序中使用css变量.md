---
title: 小程序中使用css变量
abbrlink: 27736c59
date: 2020-11-23 15:09:12
tags:
  - 钉钉小程序
  - 小程序
categories: 前端
---

在js中我们经常使用变量，但是在css中也能使用变量，你有用过吗？
今天我记录下在钉钉小程序工作台插件中使用css变量，因为我现在的工作主要是钉钉的移动端的工作台，是基于钉钉小程序开发的，然后是以单个组件的形式来开发的。
组件有可以设置很多配置项，比如：颜色，字体大小，背景等通过改变css样控制整个组件的呈现，这个时候css变量就可以派上用场了。
<!-- more -->
先来看下最终的效果图

![](1.png)
这是运行后的结果。
先说下我这程序运行的顺序：
1 组件js获取到config.json文件的配置 》 2 将所有配置变量赋值挂到xml上 》 3 css文件使用变量

``` 
// js文件
// 初始化 css变量
let {
    createLinkColor,
    createLinkBgColor,
    moreLinkColor,
    selectBgColor,
    timelineColor,
    showFoldBtn,
    initStyle,
    calendarRadiusSize,
    BgColor
} = this.props.componentProps;
let cssStyle = `
    --create-btn-color: ${createLinkColor};
    --create-btn-bgcolor: ${createLinkBgColor};
    --more-btn-color: ${moreLinkColor};
    --selected-bgc: ${selectBgColor};
    --timeline-color: ${timelineColor};
    --calendar-border-radius: ${calendarRadiusSize}rpx;
    --Bg-color: ${BgColor};
`
this.setData({
    cssVariable: cssStyle
})
```

``` 
//axml文件
<view style="{{cssVariable}}">  </view>
```

``` 
//css文件
background-color: var(--Bg-color, $text-color-white);
border-radius: var(--calendar-border-radius, 12rpx);
```

css变量有兼容问题，使用需注意，但由于我这三小程序项目，不用关心这个问题。
