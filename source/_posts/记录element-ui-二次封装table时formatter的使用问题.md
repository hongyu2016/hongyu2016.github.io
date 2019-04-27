---
title: 记录element-ui 二次封装table时formatter的使用问题
date: 2019-04-24 22:11:01
tags: [element-ui,vue]
categories: 前端
---

### 二次封装element table表格中遇到的问题记录
我们都知道element-ui 的table 中表格行的通过prop指定显示数据源中的字段，我们在进行二次封装时，如果数据都是很简单的字符串或者数字之类的，那就可以直接使用prop就行了，不需要做特殊的操作，但是如果是数组，那就得进行特殊的处理了。
我们知道element 对于表格行还提供了一个格式化的函数：formatter，常规的用法没有什么好说的了，格式化显示货币，时间等，这里要说的是当字段是数组的时候，而且要在当前列显示html元素的时候就有点蛋疼了，如果是不需要html元素，直接循环数组就行。
下面是element table的formatter的常规用法（来自element官网）：
```
<template>
  <el-table
    :data="tableData"
    style="width: 100%"
    :default-sort = "{prop: 'date', order: 'descending'}"
    >
    <el-table-column
      prop="date"
      label="日期"
      sortable
      width="180">
    </el-table-column>
    <el-table-column
      prop="name"
      label="姓名"
      sortable
      width="180">
    </el-table-column>
    <el-table-column
      prop="address"
      label="地址"
      :formatter="formatter">
    </el-table-column>
  </el-table>
</template>

<script>
  export default {
    data() {
      return {
        tableData: [{
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-04',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1517 弄'
        }, {
          date: '2016-05-01',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1519 弄'
        }, {
          date: '2016-05-03',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        }]
      }
    },
    methods: {
      formatter(row, column) {
        return row.address;
      }
    }
  }
</script>

```
下面来说下我们项目的需求
![](1.png)
如果不是二次封装也很容易解决，可以用template，但是我们这里说的是二次封装，这里只能用formatter函数了，父组件在调用时需要提供类似这样的数据：
```
tablelable:[
    {lable:'表格行1',prop:'prop_1'},
    {lable:'时间',prop:'time'},
    {lable:'附件',prop:'files',
    formatter:(row,column,cellvalue,index)={
            //在这里进行格式化输出
        }
    },
]
// 这里提供的数据源
tableData:[
    {prop_1:'***',time:'2015-20-2',files:[name:'12.jpg',url:'']},
    {prop_1:'***',time:'2015-20-2',files:[name:'12.jpg',url:'']}
]
```
下面贴出图片吧
![](2.png)
![](3.png)
这里的formatter必须要返回一个对象才能渲染出html，如果是返回一个字符串是没有用的，会原样输出字符串，比如'<a heref="">12.jpg</a>' 他不会渲染出可点击的a  而是把一整个当成字符串输出，没有渲染。<font color="red">还有一个需要注意的地方就是在这里返回的对象中的元素的属性要采用原生的写法，比如class要写成className，还有变量等也需要注意写法</font>。好了，记录下，以后再遇到就懂得处理了。
