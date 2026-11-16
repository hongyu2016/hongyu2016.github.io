---
title: react 常用组件
abbrlink: '4080e15'
date: 2021-05-17 17:42:47
tags:
  - react
  - 前端
  - 组件库
---

# react 常用组件 整理

## 零散收藏

|  组件名称   | 地址  | 简介 |
|  :----  | :----  |  :----  |
| React-classnames  | https://www.jianshu.com/p/23accf80a850 | 动态添加多个类 |
| Immutable.js  | https://www.w3cschool.cn/doc_immutable/ | 数据不可变 |
| better-scroll  | 封装例子 https://www.cnblogs.com/crazycode2/p/12589226.html | 滚动插件，可用于上拉加载、下拉刷新。主要移动端 |

---

## 按场景速查

下面这份清单来自一个真实中后台项目的依赖白名单（`.vscode/dependencies.json`，576 个包），只保留业务代码里真正会 `import` 的部分，按场景归类。版本号是该项目的实际锁定值，仅作参考——新项目按需取最新，但对齐 antd / React 大版本时这些值能少踩很多坑。

### 一、UI 组件库与表格

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `antd` | 6.2.1 | PC 中后台首选，表单、表格、弹窗、布局全覆盖；v5 起用 CSS-in-JS，无需再引样式文件 |
| `antd-mobile` | 5.42.3 | 移动端组件库，与 antd 同一套设计语言，但组件完全不同 |
| `@ant-design/pro-table` | — | 在 antd Table 上封装的「搜索表单 + 工具栏 + 分页」一体组件，中后台列表页能省一半代码 |
| `ali-react-table` | — | 大数据量表格，虚拟滚动性能好，适合几千行以上 |
| `virtualizedtableforantd4` | — | 给 antd v4 的 Table 加虚拟滚动（v5 已内置 `virtual`） |
| `rc-*` / `@rc-component/*` | — | antd 的底层原子组件（`rc-select`、`rc-table`…），想脱离 antd 单独用某个交互时才会碰 |
| `element-ui` / `vux` | — | Vue 2 生态的组件库，只在维护老项目时出现 |

### 二、图表与可视化

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `echarts` | 5.4.3 | 图表事实标准，配置项最全；包体大，建议按需引入或走 CDN |
| `echarts-for-react` | 3.0.1 | echarts 的 React 薄封装，负责 `init / setOption / resize / dispose` 生命周期 |
| `@ant-design/plots` | 1.2.5 | 基于 G2 的图表库，API 比 echarts 简单，适合常规业务图表 |
| `@ant-design/charts` | 1.4.2 | charts 全家桶（含 plots、流程图），体积大，按需引 |
| `@antv/g2` / `@antv/g2plot` | — | 蚂蚁可视化底层与图表层，想深度定制时直接用它 |
| `d3` | 7.8.4 | 自由度高但学习曲线陡，做定制可视化/坐标系时用 |
| `echarts-liquidfill` / `echarts-wordcloud` | — | 水波球、词云等扩展图表 |

### 三、关系图 / 流程图 / 拓扑

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `@antv/g6` | 4.8.23 | 关系图、拓扑图首选，内置布局与交互 |
| `@antv/graphin` | — | 基于 G6 的 React 图谱组件，配套 `graphin-components` 开箱即用 |
| `@xyflow/react` | 12.8.1 | 即 React Flow v12，节点连线式画布（流程编排、审批流）最好用 |
| `mxgraph` / `@typed-mxgraph/typed-mxgraph` | — | 老牌绘图引擎，流程图/建模工具，体积大、API 老派 |
| `@dagrejs/dagre` / `elkjs` | — | 自动布局算法，配合上面几个画布库算节点坐标 |

### 四、状态管理

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `zustand` | 4.5.2 | 轻量首选，无 Provider、无样板代码，中小项目几乎零成本 |
| `redux` + `react-redux` | 5.0.1 / 9.2.0 | 老牌方案，配合 DevTools 调试体验最好，样板代码多 |
| `@reduxjs/toolkit` | 2.11.2 | Redux 官方推荐的写法，`createSlice` 消掉大部分模板代码 |
| `@rematch/core` | 1.4.0 | 基于 Redux 的 model 化封装，Vue 用户会觉得像 vuex |
| `immer` / `use-immer` | 9.0.19 | 用可变写法产出不可变数据，Redux Toolkit 已内置 |
| `immutability-helper` / `immutable` | — | 老项目的不可变工具，新项目一般用 immer 替代 |
| `vuex` | — | Vue 侧对应物 |

### 五、路由

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `react-router-dom` | 7.12.0 | v6 之后 `Switch` 换成 `Routes`，数据路由（loader/action）是 v6.4+ 的新能力 |
| `react-router-config` | 4.4.0-beta | 用配置数组描述路由，适合权限动态路由，v6 之后基本被 `createBrowserRouter` 取代 |
| `history` | — | 脱离 React 操作 History（微前端里基座独占 History 时会用到） |
| `vue-router` | — | Vue 侧对应物 |

### 六、网络请求

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `axios` | 0.18.0 | 注意：这是很老的版本，1.x 的默认导出与拦截器签名有变化；新项目直接上 1.x |
| `qs` | 6.10.2 | 复杂嵌套对象的 query 序列化（`a[b][c]=1`），后端是 PHP/Java 老框架时几乎必需 |
| `query-string` | 8.1.0 | 更现代的 query 解析，API 比 qs 干净 |
| `@microsoft/fetch-event-source` | 2.0.1 | 基于 fetch 的 SSE 客户端，支持 POST、自定义 header、断线重连；比原生 `EventSource` 实用得多 |
| `axios-jsonp-pro` | — | 只为了绕跨域的历史产物，现在有 CORS 基本用不上 |

### 七、表单引擎与低代码

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `@formily/antd` | 2.2.12 | 表单领域模型方案，配合 `@formily/react`、`@formily/reactive-react` 做复杂联动表单 |
| `@designable/react` + `@designable/core` | — | 拖拽式设计器底座，阿里低代码引擎的前身 |
| `@designable/formily-setters` / `formily-transformer` | — | 把 Formily schema 接进设计器 |
| `fr-generator` | — | 基于 Formily 的表单/JSON Schema 生成器 |
| `async-validator` | — | 表单校验内核，antd Form 底层就是它 |

### 八、富文本与代码编辑器

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `quill` | 1.3.7 | 轻量富文本，2.0 长期未正式发版，1.3.7 最稳 |
| `lexical` / `@lexical/react` | 0.28.0 | Meta 出品的编辑器框架，可扩展性强，适合做定制编辑器 |
| `@uiw/react-codemirror` | 4.23.6 | CodeMirror 6 的 React 封装，轻、快，适合配置/表达式编辑 |
| `monaco-editor` | 0.36.1 | VS Code 同款，功能最全但体积大（几 MB），适合做「代码编辑」场景 |
| `react-ace` / `ace-builds` | — | 更老的代码编辑器方案 |
| `markdown-it` / `react-markdown` | 14.1.0 / 9.0.0 | Markdown 渲染，前者产出 HTML，后者直接输出 React 元素 |
| `prismjs` / `highlight.js` | 1.28.0 / 11.8.0 | 代码高亮，配合 Markdown 用 |
| `react-syntax-highlighter` | 16.1.0 | 高亮的 React 封装，省去手动 `dangerouslySetInnerHTML` |

### 九、拖拽、缩放与滚动

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `@dnd-kit/core` + `@dnd-kit/sortable` | 6.3.1 | 新一代拖拽方案，支持键盘与无障碍，维护活跃，优先选它 |
| `react-dnd` | 16.0.1 | 基于 HTML5 拖放 API，坑多且维护乏力，老项目常见 |
| `sortablejs` / `react-sortablejs` / `vuedraggable` | 1.15.0 | 直接操作 DOM 的列表排序，简单场景够用 |
| `react-rnd` | 10.4.1 | 可拖拽 + 可缩放的容器（弹窗、画布元素） |
| `re-resizable` / `react-resizable` | 6.9.11 | 只缩放不拖拽，面板分栏拖拽调宽度时用 |
| `react-custom-scrollbars` / `@better-scroll/*` | — | 自定义滚动条与移动端滚动增强（下拉刷新、上拉加载、轮播） |

### 十、长列表与虚拟滚动

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `react-window` | 1.8.11 | 轻量虚拟列表，API 简单，几千行首选 |
| `react-virtualized` | 9.22.5 | 功能最全（列表/表格/网格），但 API 复杂、包体大 |
| `react-virtuoso` | 4.6.0 | 自动高度、动态加载体验最好，聊天记录类场景很合适 |
| `react-cool-virtual` / `rc-virtual-list` | — | 前者基于 hook，后者是 antd 内部在用的实现 |
| `react-infinite-scroll-component` / `react-lazyload` | — | 滚动触底加载、图片懒加载的轻量方案 |
| `better-scroll` | 2.0.5 | 移动端滚动内核，iOS/Android 惯性滚动手感统一 |

### 十一、日期与时间

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `dayjs` | 1.11.12 | 首选，2KB、API 与 moment 兼容，antd 5/6 默认用它 |
| `moment` | 2.30.1 | 老项目常见，体积大、官方已停止新特性开发，新项目别引 |
| `@fullcalendar/react` | 6.1.20 | 日程/月历视图，配合 `daygrid`、`timegrid`、`interaction` 插件 |
| `@event-calendar/*` | — | 另一套日历组件，资源视图见长 |
| `rrule` | 2.7.1 | 重复规则（每周一、每月末），日程系统的必备件 |
| `cron-parser` / `cron-expression-validator` | — | 解析与校验 cron 表达式，定时任务配置界面用 |
| `solarlunar` | — | 农历转换（节假日、生肖、节气） |

### 十二、文件、图片、打印与二维码

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `xlsx` | 0.18.5 | 读写 Excel，注意新版本已迁出 npm 官方仓库 |
| `@grapecity/spread-sheets` | 16.2.2 | 在线 Excel 编辑器（类飞书表格），功能最全但需商业授权 |
| `js-export-excel` / `file-saver` | 1.1.4 / 2.0.5 | 简单导出与浏览器端存文件 |
| `html2canvas` / `html-to-image` | 1.4.1 / 1.11.11 | DOM 转图片（截图、分享海报），跨域图片会踩坑 |
| `jspdf` / `react-to-print` | 2.5.2 | 前端导出 PDF、调用打印 |
| `qrcode` / `qrcode.react` / `jsbarcode` | 1.5.0 | 二维码、条形码生成 |
| `react-easy-crop` / `react-cropper` | 4.7.4 | 头像裁剪，前者手势体验好，后者功能更全 |
| `exif-js` / `image-size` | — | 读 EXIF 方向信息（手机拍照会横躺）、取图片宽高 |

### 十三、工具函数

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `lodash` / `lodash-es` | 4.17.23 | 防抖节流、深拷贝、对象路径操作；`lodash-es` 支持 tree-shaking |
| `classnames` | 2.5.1 | 条件拼 className，写组件必装 |
| `uuid` / `nanoid` | 9.0.1 / 3.3.11 | 唯一 ID，`nanoid` 体积只有几百字节 |
| `crypto-js` / `jsencrypt` / `js-base64` | 4.1.1 / 3.0.0-rc.1 / 3.6.0 | 加解密、RSA、Base64，密码传输与签名常用 |
| `js-md5` / `spark-md5` | — | 文件分片上传要算 md5，大文件用 `spark-md5` 增量算 |
| `pinyin-pro` | 3.13.1 | 汉字转拼音，通讯录分组、搜索联想用 |
| `change-case` / `omit.js` / `flat` / `is-url` | — | 大小写转换、剔除字段、扁平化、URL 判断等小工具 |
| `lz-string` | — | 字符串压缩后存 localStorage，避免超配额 |
| `clipboard` / `copy-to-clipboard` | 2.0.11 | 一键复制 |
| `ahooks` / `react-use` | 3.9.6 / 17.4.2 | 常用 hooks 合集，能省掉大量样板（分页、防抖、监听、请求） |
| `pubsub-js` / `@evecalm/message-hub` | — | 跨组件事件总线，微前端里做应用间通信 |

### 十四、动画与交互反馈

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `@react-spring/web` | 10.0.3 | 物理弹簧动画，过渡自然，React 生态首选 |
| `react-motion` | 0.5.2 | 老牌动画库，维护停滞，新项目不建议 |
| `react-transition-group` | 4.4.5 | 入场/离场过渡，配合 CSS 用 |
| `animate.css` | 3.7.2 | 现成的 CSS 动画类，加个 class 就能用 |
| `nprogress` | 0.2.0 | 顶部进度条，路由切换时用 |
| `react-countup` / `typewriter-effect` | — | 数字滚动、打字机效果，数据大屏常见 |
| `intro.js-react` | — | 新手引导 / 功能高亮 |

### 十五、移动端、地图与外部 SDK

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `swiper` / `react-slick` / `slick-carousel` | 8.4.5 / 0.31.0 | 轮播，`swiper` 手感更好，`react-slick` 更贴合 React |
| `pullltorefreshjs` / `inobounce` | — | 下拉刷新、禁用 iOS 橡皮筋回弹 |
| `@amap/amap-jsapi-loader` | 1.0.1 | 高德地图加载器，配合 `react-bmapgl` 使用 |
| `react-bmapgl` | — | 百度地图的 React 封装 |
| `dingtalk-jsapi` / `weixin-js-sdk` / `wps-jsapi` / `gdt-jsapi` | — | 钉钉、微信、WPS、广点通的 JSAPI，做企业内嵌应用时绕不开 |

### 十六、国际化

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `react-i18next` + `i18next` | 12.1.5 / 22.4.9 | 生态最完整，支持命名空间、按需加载语言包 |
| `react-intl` | 2.7.2 | ICU 消息语法规范，配合 `babel-plugin-react-intl` 做文案抽取 |
| `vue-i18n` | — | Vue 侧对应物 |

### 十七、工程化与质量

| 库 | 参考版本 | 说明 |
| --- | --- | --- |
| `webpack` | 5.94.0 | 模块打包，`Module Federation` 是 v5 新增能力 |
| `@swc/core` / `swc-loader` | 1.7.40 | 用 Rust 换掉 babel，构建速度提升明显 |
| `sass` / `less` | 1.43.5 / 3.9.0 | CSS 预处理器，`sass` 用 dart-sass（`node-sass` 已废弃） |
| `typescript` | 5.8.2 | 类型系统 |
| `eslint` + `@typescript-eslint/*` | 7.32.0 | 代码规范，v9 起换成 `eslint.config.js` 扁平配置 |
| `husky` / `lint-staged` / `@commitlint/cli` | 9.1.7 | 提交前校验与提交信息规范 |
| `vconsole` | 3.15.1 | 移动端真机上的控制台，排查线上问题神器 |
| `web-vitals` | — | 采集 LCP、CLS 等真实用户性能指标 |
| `webpack-bundle-analyzer` | — | 打包体积分析，找「谁把包撑大了」 |

---

## 几条选型经验

1. **同类的库只留一个**。见过一个项目同时引了 `echarts`、`g2plot`、`@ant-design/charts`，三个图表库加起来 2MB+，而业务上只需要一个柱状图。选一个主力，其余特殊情况单独按需加载。

2. **日期库跟 UI 库对齐**。antd v5 之后默认 dayjs，再引 moment 就等于白送 70KB，而且两个库的 `locale` 机制完全不同，切换语言时会发现一半组件不生效。

3. **表格超过 1000 行就别硬渲染**。antd Table 默认全量渲染 DOM，几千行直接卡死。要么开 `virtual`（v5 内置），要么换 `ali-react-table`。

4. **拖拽优先 dnd-kit**。`react-dnd` 最后一次实质更新很早，HTML5 后端在触屏上坑很多；`dnd-kit` 有键盘支持和无障碍，API 也更符合现代 React 习惯。

5. **SSE 别手写 EventSource**。原生 `EventSource` 不支持自定义 header（想带 token 只能塞 query），断线重连逻辑也要自己写。`@microsoft/fetch-event-source` 两个问题都解决了。

6. **组件库只该有一个实例**。尤其在微前端里：基座通过 `externals` 把 React 和 antd 外置，微应用不再打包自己的副本，否则页面上会同时存在两套 antd 的 CSS-in-JS 上下文，弹窗层级、主题变量都会错乱。

7. **体积先看分析器再优化**。`webpack-bundle-analyzer` 跑一次，经常能发现「整个 lodash 被引进来了」或「某个图表库被三个页面重复打包」，改一行 import 就能省几百 KB。
