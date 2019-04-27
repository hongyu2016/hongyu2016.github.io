---
title: element-ui 表格中的form 的inpu动态t验证规则
date: 2019-04-27 21:23:46
tags: [element-ui,vue]
categories: 前端
---
### 表格中的input需要做某些校验，比如，“本次报销金额”要小于“未报销金额”，这就有点麻烦了，我们都知道，element的form的验证规则rules都是在初始化的时候就定好了的，比如时间验证：
```
date2: [
    { type: 'date', required: true, message: '请选择时间', trigger: 'change' }
],
```
而我们项目中的是这样的:
![](4.png) 又不想自定义指令来做，还是看下element能不能做吧，于是就开始看文档，发现只能用自定义rules，但是自定义指令，写死的规则也不符合要求啊，因为表格的数据都是不固定的，
首先我们把表格的数据源字段移到我们的form中
![](1.png)
![](2.png)
![](3.png)
然后自定义规则：
![](5.png)
这里需要用到一些技巧，比如在模板中，
```
:prop="`inputTaxTable.${scope.$index}.thisApplyMoney`"
```
在需要校验的列中，`inputTaxTable.${scope.$index}.thisApplyMoney` 查找当前行的数据源，

以及查找“未报销金额”这行的数据来跟当前金额进行对比的时候，保证找到的当前行的数据。
```
 //field字段中包含了该行表格的index
    let field=rule.field;//规则名称
    let _fieldArr=[];
    _fieldArr=field.split('.');
    let _index=_fieldArr[1];//得到表格的第n行

    if(Number.parseFloat(value)>Number.parseFloat(this.ruleForm.inputTaxTable[_index].notApplyMoney)){
        return callback(new Error('不可大于未报销金额'));
    }
}
```

