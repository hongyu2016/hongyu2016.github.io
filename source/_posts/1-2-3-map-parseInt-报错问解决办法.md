---
title: '["1", "2", "3"].map(parseInt)报错问题解决办法'
tags: js
categories: 前端
abbrlink: 1428c068
date: 2019-03-30 11:41:08
---

第一次使用 `["1", "2", "3"].map(parseInt)`时，以为会得到[1,2,3]这个结果，结果很不幸得到的是[1,NaN,NaN]，为什么会这样呢，其实是因为map函数传递的是三个参数`value,index,arry`,而parseInt函数传递的是`value,radix`，`radix`不传时默认使用10，表示十进制，但是如果radix在2-36之外会返回NaN。
好了，既然已经知道了原因，那么应该怎么修改，才能得到正确的结果呢，
<!-- more -->
很简单，把map和parseIntDe完整参数写出来就可以了`["1", "2", "3"].map((value,index,array)=>parseInt(value,10));` 给parseInt指定10进制，用es5的写法则是
```
["1", "2", "3"].map(function(value,index,array){
	return parseInt(value,10)
});  //返回 [1,2,3]
```