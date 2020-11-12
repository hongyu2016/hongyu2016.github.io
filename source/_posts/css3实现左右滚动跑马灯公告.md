---
title: css3实现左右滚动跑马灯公告
tags:
  - 小程序
  - 钉钉小程序
categories: 前端
abbrlink: 90c577a3
date: 2020-11-09 17:58:27
---

### 通常的做法

#### 1：js定时器
由于这次做的是钉钉工作台自定义组件，钉钉不建议使用定时器，会影响性能
#### 2：css3 动画实现
css3动画流畅不卡顿。需要解决3个问题：
1：循环滚动，且最后一条滚动完之后紧接着第一条出现
2：滚动速度可调
3：内容没有超过滚动区时，不需要滚动
<!-- more -->
要实现的效果图如下图所示：
![](1.png)

对于循环滚动，如果按照一般做法，是没有办法做到 最后一条滚动完之后紧接着第一条出现的，那么怎么办呢？再复制一个滚动列表就可以了，第一个列表开始滚动时，第二个列表延时滚动，滚动速度一样，这样当第一个列表滚动完以后，第二个列表就会紧接着出现了。
对于滚动的速度，因为滚动的宽度是不定的，是根据接口返回的内容决定的，但是 对于animation动画来说，宽度是一定的，改变整个动画的时间即可达到变速的效果，需要使用钉钉的查看元素的信息api去获取各个元素的宽度，动态设置速度。
下面是关键代码 

``` 
// js
methods: { 
    async dealData() {
      let _sysInfo = await getSdk().getSystemInfo() || {};
      let { windowWidth } = _sysInfo;
      let rpxToPxRatio = windowWidth / 750;

      let { widthControl, marqueeLogic, marqueeSpeed } = this.props.componentProps;
      // 需要先将列表的最大宽度设置为屏幕的宽度
      this.setData({
        listWidth: `--listMaxWidth: ${windowWidth}px;`
      })
      const query = my.createSelectorQuery();

      query
        .selectAll(`.marquee-notice .content`)
        .boundingClientRect()
        .selectAll(`.marquee-notice .list-group .list`)
        .boundingClientRect()
        .selectAll(`.marquee-notice .content-left`)
        .boundingClientRect()
        .exec(res => {
          console.error(res);
          let contentWidth = res[0][0].width;
          let _arr = res[1];
          let _width = 0;
          let plusWidth = res[2][0].width; // plusWidth表示每一行滚动的总距离
          for (let i = 0; i < _arr.length; i++) {
            _width += (_arr[i].width)
          }

          let $_width = _width * 2 - plusWidth >= contentWidth ? _width * 2 - plusWidth : contentWidth;

          let speed = $_width >= (contentWidth * 2) ? (0.0187) : (0.0187 * 2); // 如果总的列表宽度大于等于 content 宽度的两倍（因为是两个列表动画）则使用3 / 161速率，否则使用6 / 161 。速度不能百分比准确
          let groupStyle_1 = '';
          let groupStyle_2 = '';
          console.error('list宽度和content宽度', _width, contentWidth)
          if (marqueeLogic === 1 || marqueeLogic === 2 && (_width + 10) > contentWidth) {
            // 无论是否超过内容宽度，都滚动
            groupStyle_1 = `
              animation: run ${$_width * (speed / marqueeSpeed)}s linear 0s infinite;
              --width:${-(parseInt($_width))}px;
              --leftPosition: ${plusWidth}px;
            `;
            groupStyle_2 = `
              animation: run2 ${$_width * (speed / marqueeSpeed)}s linear ${$_width * (speed / marqueeSpeed) / 2}s infinite;
              --width:${-(parseInt($_width))}px;
              --leftPosition: ${plusWidth}px;          
            `;
          } else {
            // 只有超过content宽度才滚动
            groupStyle_1 = 'transform: translateX(0);';
          }
          this.setData({
            groupStyle_1: groupStyle_1,
            groupStyle_2: groupStyle_2,
          })

        });

    },
}
```
```
// css 关键代码：
.list-group-2 {
    display: flex;
    position: absolute;
    width: 100%;
    // animation: run2 linear 24s infinite;
    // animation-delay:12s; 因为变量是通过js查询的，存在异步，所以只能写行内样式
    transform: translateX(var(--leftPosition, 375px));
}
// 第一行滚动文本
@keyframes run {

    0% {
    transform: translateX(var(--leftPosition, 375px));
    opacity: 1;

    }

    1% {
    opacity: 1;
    }

    99% {
    opacity: 1;
    }

    100% {
    transform: translateX(var(--width, -600px));
    opacity: 0;
    }
}

// 第二行滚动文本
@keyframes run2 {

    0% {
    transform: translateX(var(--leftPosition, 375px));

    }

    1% {
    opacity: 1;
    }

    99% {
    opacity: 1;
    }

    100% {
    transform: translateX(var(--width, -600px));
    }
}
```
```
// axml
<view class="content" style="{{listWidth}}">
    <view class="list-group"  style="{{groupStyle_1}}">
    <block a:for="{{list}}" a:key="{{i}}" a:for-index="i">
        <view class="list {{componentProps.widthControl === 1 ? 'max-width' : ''}}" onTap="handleTapLink" data-url="{{item.link}}">
        <view class="list-text">{{item.title}}</view>
        </view>
    </block>
    </view>
    <view class="list-group-2"  style="{{groupStyle_2}}">
    <block a:for="{{list}}" a:key="{{i}}" a:for-index="i">
        <view class="list {{componentProps.widthControl === 1 ? 'max-width' : ''}}" onTap="handleTapLink" data-url="{{item.link}}" style="{{componentProps.widthControl === 1 ? 'max-width: var(--listMaxWidth, 4rem)':''}}">
        <view class="list-text">{{item.title}}</view>
        </view>
    </block>
    </view>
</view>
```