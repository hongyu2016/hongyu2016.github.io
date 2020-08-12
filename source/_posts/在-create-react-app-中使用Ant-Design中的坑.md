---
title: 在 create-react-app 中使用Ant Design中的坑
abbrlink: 204a2bf8
date: 2020-06-04 18:51:08
tags: react,
categories: 前端
---

## Ant Design 中的文档有错误，参数变了

这是原来的配置：
<!-- more -->
```
// craco.config.js
const CracoLessPlugin = require('craco-less');

module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          modifyVars: { '@primary-color': '#1DA57A' },
          javascriptEnabled: true,
        },
      },
    },
  ],
};
```

这是正确的配置：

```
// craco.config.js
const CracoLessPlugin = require('craco-less');

module.exports = {
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
           lessOptions:{
                modifyVars: { '@primary-color': '#1DA57A' },
                javascriptEnabled: true,
            }
        },
      },
    },
  ],
};
```
