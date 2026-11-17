---
title: vue/react项目中常用的前端组件库
abbrlink: '4080e15'
date: 2021-05-17 17:42:47
tags:
  - react
  - vue
  - 前端
  - 组件库
categories:
  - 前端
---

# vue/react 项目中常用的前端组件库 整理

> 表格里 **框架** 列的含义：`React` = 只适用于 React；`Vue` = 只适用于 Vue；`通用` = 框架无关，两边都能用。每个库独占一行，功能相近的排在相邻行。

## 按场景速查

下面这份清单整理自真实项目中的实践经验，只保留业务代码里真正会 `import` 的部分，按场景归类。所列版本仅作参考——新项目按需取最新，但对齐 antd / Element Plus / React / Vue 大版本时这些值能少踩很多坑。
<!-- more -->

### 一、UI 组件库与表格

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `antd` | React | 6.2.1 | PC 中后台首选，表单、表格、弹窗、布局全覆盖；v5 起用 CSS-in-JS，无需再引样式文件 |
| `antd-mobile` | React | 5.42.3 | 移动端组件库，与 antd 同一套设计语言，但组件完全不同 |
| `@ant-design/pro-table` | React | — | 在 antd Table 上封装的「搜索表单 + 工具栏 + 分页」一体组件，中后台列表页能省一半代码 |
| `ali-react-table` | React | — | 大数据量表格，虚拟滚动性能好，适合几千行以上 |
| `virtualizedtableforantd4` | React | — | 给 antd v4 的 Table 加虚拟滚动（v5 已内置 `virtual`） |
| `rc-*` | React | — | antd 的底层原子组件系列（`rc-select`、`rc-table`…），想脱离 antd 单独用某个交互时才会碰 |
| `@rc-component/*` | React | — | antd 新版底层原子组件系列，作用同上，是近年重构后的新作用域 |
| `element-ui` | Vue | — | Element 的 Vue 2 组件库，中后台老项目常见 |
| `element-plus` | Vue | — | Element 的 Vue 3 版本，国内最流行的 Vue 组件库，中后台首选 |
| `vux` | Vue | — | 早期基于微信生态的 Vue 移动端组件库，现已基本停止维护，新项目别用 |
| `ant-design-vue` | Vue | — | Ant Design 的 Vue 实现，企业级中后台，设计规范专业 |
| `naive-ui` | Vue | — | TypeScript 优先、零外部依赖、主题系统灵活，尤雨溪推荐，数据密集场景的表格/表单很强 |
| `arco-design-vue` | Vue | — | 字节跳动出品的企业级设计系统，组件丰富、主题定制强 |
| `tdesign-vue-next` | Vue | — | 腾讯出品的 Vue 3 组件库，跨端一致、企业级品质 |
| `vuetify` | Vue | — | Material Design 风格，功能最全、无障碍支持好，国外流行 |
| `primevue` | Vue | — | 组件数量极多（90+），主题系统强，支持 unstyled mode 自由定制 |
| `shadcn-vue` | Vue | — | 非 npm 包：把组件源码复制进项目自己改，完全掌控样式；基于 Reka UI + Tailwind |
| `reka-ui`（原 `radix-vue`） | Vue | — | 无头组件底座，只提供交互与无障碍、不带样式，`shadcn-vue` 的底层 |
| `vant` | Vue | — | 有赞出品，国内最流行的 Vue 移动端组件库，轻量、组件齐，支持 H5/小程序 |
| `nutui` | Vue | — | 京东出品，电商场景见长，适配 H5 / 小程序 / App |
| `varlet` | Vue | — | Material Design 风格的 Vue 3 移动端组件库，支持暗黑模式 |
| `cube-ui` | Vue | — | 滴滴出品的移动端组件库 |
| `vue-final-modal` | Vue | — | 弹窗/模态框管理，支持嵌套、堆叠与拖拽，比手写 `v-if` 弹窗好维护 |

### 二、图表与可视化

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `echarts` | 通用 | 5.4.3 | 图表事实标准，配置项最全；包体大，建议按需引入或走 CDN |
| `echarts-for-react` | React | 3.0.1 | echarts 的 React 薄封装，负责 `init / setOption / resize / dispose` 生命周期 |
| `@ant-design/plots` | React | 1.2.5 | 基于 G2 的图表库，API 比 echarts 简单，适合常规业务图表 |
| `@ant-design/charts` | React | 1.4.2 | charts 全家桶（含 plots、流程图），体积大，按需引 |
| `@antv/g2` | 通用 | — | 蚂蚁的图形语法可视化底层，想深度定制图表时直接用它 |
| `@antv/g2plot` | 通用 | — | 基于 G2 的图表封装，开箱即用，比直接写 G2 简单 |
| `d3` | 通用 | 7.8.4 | 自由度高但学习曲线陡，做定制可视化/坐标系时用 |
| `echarts-liquidfill` | 通用 | — | ECharts 的水波球扩展图表 |
| `echarts-wordcloud` | 通用 | — | ECharts 的词云扩展图表 |
| `vue-chartjs` | Vue | — | Chart.js 的 Vue 封装，轻量图表场景 |

### 三、关系图 / 流程图 / 拓扑

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `@antv/g6` | 通用 | 4.8.23 | 关系图、拓扑图首选，内置布局与交互 |
| `@antv/graphin` | React | — | 基于 G6 的 React 图谱组件，配套 `graphin-components` 开箱即用 |
| `@xyflow/react` | React | 12.8.1 | 即 React Flow v12，节点连线式画布（流程编排、审批流）最好用 |
| `mxgraph` | 通用 | — | 老牌绘图引擎，流程图/建模工具，体积大、API 老派 |
| `@typed-mxgraph/typed-mxgraph` | 通用 | — | `mxgraph` 的 TypeScript 类型封装，方便在 TS 项目里调用 |
| `@dagrejs/dagre` | 通用 | — | 有向图自动布局算法，配合画布库算节点坐标 |
| `elkjs` | 通用 | — | Eclipse 布局引擎，同样用于自动排布节点，复杂图效果更好 |
| `@antv/x6` | 通用 | — | 图编辑引擎，流程图 / ER 图 / DAG，比 `g6` 更偏「可编辑建模」 |

### 四、状态管理

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `zustand` | React | 4.5.2 | 轻量首选，无 Provider、无样板代码，中小项目几乎零成本 |
| `redux` | 通用 | 5.0.1 | 老牌状态管理内核，配合 DevTools 调试体验最好，样板代码多 |
| `react-redux` | React | 9.2.0 | `redux` 的 React 绑定，提供 `Provider` 与 `useSelector` / `useDispatch` |
| `@reduxjs/toolkit` | 通用 | 2.11.2 | Redux 官方推荐的写法，`createSlice` 消掉大部分模板代码 |
| `@rematch/core` | 通用 | 1.4.0 | 基于 Redux 的 model 化封装，另有 `@rematch/vue`，Vue 用户会觉得像 vuex |
| `immer` | 通用 | 9.0.19 | 用可变写法产出不可变数据，Redux Toolkit 已内置 |
| `use-immer` | React | — | `immer` 的 React hook 封装，配合 `useState` 管理状态 |
| `immutable` | 通用 | — | 即 Immutable.js，不可变数据结构，老项目常见；文档 https://www.w3cschool.cn/doc_immutable/；新项目一般用 immer 替代 |
| `immutability-helper` | 通用 | — | 用简洁语法对不可变对象做增改删的老牌工具，新项目一般用 immer 替代 |
| `vuex` | Vue | — | Vue 2/3 官方状态管理；Vue 3 新项目官方更推荐 Pinia |
| `pinia` | Vue | — | Vue 3 官方状态管理，Vuex 继任者，约 1KB、类型友好、无需嵌套模块，新项目默认选它 |
| `pinia-plugin-persistedstate` | Vue | — | Pinia 状态持久化插件，刷新页面自动恢复 store |

### 五、路由

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `react-router-dom` | React | 7.12.0 | v6 之后 `Switch` 换成 `Routes`，数据路由（loader/action）是 v6.4+ 的新能力 |
| `react-router-config` | React | 4.4.0-beta | 用配置数组描述路由，适合权限动态路由，v6 之后基本被 `createBrowserRouter` 取代 |
| `history` | 通用 | — | 脱离 React 操作 History（微前端里基座独占 History 时会用到） |
| `vue-router` | Vue | — | Vue 官方路由管理器，实现单页应用的页面导航，Vue 3 用 v4 |

### 六、网络请求

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `axios` | 通用 | 0.18.0 | 注意：这是很老的版本，1.x 的默认导出与拦截器签名有变化；新项目直接上 1.x |
| `qs` | 通用 | 6.10.2 | 复杂嵌套对象的 query 序列化（`a[b][c]=1`），后端是 PHP/Java 老框架时几乎必需 |
| `query-string` | 通用 | 8.1.0 | 更现代的 query 解析，API 比 qs 干净 |
| `@microsoft/fetch-event-source` | 通用 | 2.0.1 | 基于 fetch 的 SSE 客户端，支持 POST、自定义 header、断线重连；比原生 `EventSource` 实用得多 |
| `axios-jsonp-pro` | 通用 | — | 只为了绕跨域的历史产物，现在有 CORS 基本用不上 |

### 七、表单引擎与低代码

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `@formily/antd` | React | 2.2.12 | 表单领域模型方案，配合 `@formily/react`、`@formily/reactive-react` 做复杂联动表单 |
| `@designable/react` | React | — | 拖拽式设计器的 React 实现，阿里低代码引擎的前身 |
| `@designable/core` | 通用 | — | 设计器内核，负责拖拽编排与 schema 模型，框架无关 |
| `@designable/formily-setters` | 通用 | — | 把 Formily 的属性配置面板接进设计器 |
| `formily-transformer` | 通用 | — | Formily schema 与设计器 JSON 之间的转换工具 |
| `fr-generator` | 通用 | — | 基于 Formily 的表单/JSON Schema 生成器 |
| `async-validator` | 通用 | — | 表单校验内核，antd Form 和 Element UI/Plus 的 validate 底层都是它 |
| `vee-validate` | Vue | — | Vue 表单校验，支持 Composition API 与多语言，配合 `yup` / `zod` 做 schema 校验 |

### 八、富文本与代码编辑器

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `quill` | 通用 | 1.3.7 | 轻量富文本编辑器，2.0 长期未正式发版，1.3.7 最稳 |
| `lexical` | 通用 | 0.28.0 | Meta 出品的编辑器内核，可扩展性强，适合做定制编辑器 |
| `@lexical/react` | React | — | `lexical` 的 React 绑定层 |
| `@uiw/react-codemirror` | React | 4.23.6 | CodeMirror 6 的 React 封装，轻、快，适合配置/表达式编辑 |
| `monaco-editor` | 通用 | 0.36.1 | VS Code 同款，功能最全但体积大（几 MB），适合做「代码编辑」场景 |
| `react-ace` | React | — | Ace 编辑器的 React 封装，较老的代码编辑器方案 |
| `ace-builds` | 通用 | — | Ace 编辑器的构建产物，`react-ace` 的底层依赖 |
| `markdown-it` | 通用 | 14.1.0 | Markdown 渲染库，产出 HTML 字符串 |
| `react-markdown` | React | 9.0.0 | Markdown 渲染库，直接输出 React 元素 |
| `prismjs` | 通用 | 1.28.0 | 轻量代码高亮库，配合 Markdown 用 |
| `highlight.js` | 通用 | 11.8.0 | 老牌代码高亮库，开箱自动识别语言 |
| `react-syntax-highlighter` | React | 16.1.0 | 高亮的 React 封装，省去手动 `dangerouslySetInnerHTML` |

### 九、拖拽、缩放与滚动

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `@dnd-kit/core` | React | 6.3.1 | 新一代拖拽方案的内核，支持键盘与无障碍，维护活跃，优先选它 |
| `@dnd-kit/sortable` | React | — | `dnd-kit` 的列表排序扩展，做拖拽排序时配合 core 用 |
| `react-dnd` | React | 16.0.1 | 基于 HTML5 拖放 API，坑多且维护乏力，老项目常见 |
| `sortablejs` | 通用 | 1.15.0 | 拖拽排序内核，直接操作 DOM，框架无关 |
| `react-sortablejs` | React | — | `sortablejs` 的 React 封装，简单列表排序够用 |
| `vuedraggable` | Vue | — | `sortablejs` 的 Vue 封装，Vue 项目里性价比最高的拖拽排序选择 |
| `react-rnd` | React | 10.4.1 | 可拖拽 + 可缩放的容器（弹窗、画布元素） |
| `re-resizable` | React | 6.9.11 | 只缩放不拖拽，面板分栏拖拽调宽度时用 |
| `react-resizable` | React | — | 更早的可缩放组件方案，功能类似 `re-resizable` |
| `react-custom-scrollbars` | React | — | React 的自定义滚动条组件 |
| `@better-scroll/*` | 通用 | — | 移动端滚动增强，支持下拉刷新、上拉加载、轮播，框架无关 |

### 十、长列表与虚拟滚动

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `react-window` | React | 1.8.11 | 轻量虚拟列表，API 简单，几千行首选 |
| `react-virtualized` | React | 9.22.5 | 功能最全（列表/表格/网格），但 API 复杂、包体大 |
| `react-virtuoso` | React | 4.6.0 | 自动高度、动态加载体验最好，聊天记录类场景很合适 |
| `react-cool-virtual` | React | — | 基于 hook 的虚拟滚动方案 |
| `rc-virtual-list` | React | — | antd 内部在用的虚拟列表实现 |
| `react-infinite-scroll-component` | React | — | 滚动触底加载更多的轻量方案 |
| `react-lazyload` | React | — | 图片懒加载的轻量方案 |
| `better-scroll` | 通用 | 2.0.5 | 移动端滚动内核，支持上拉加载/下拉刷新，iOS/Android 惯性滚动手感统一；封装例子 https://www.cnblogs.com/crazycode2/p/12589226.html |

### 十一、日期与时间

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `dayjs` | 通用 | 1.11.12 | 首选，2KB、API 与 moment 兼容，antd 5/6 默认用它 |
| `moment` | 通用 | 2.30.1 | 老项目常见，体积大、官方已停止新特性开发，新项目别引 |
| `@fullcalendar/react` | React | 6.1.20 | 日程/月历视图，配合 `daygrid`、`timegrid`、`interaction` 插件 |
| `@event-calendar/*` | 通用 | — | 另一套日历组件，资源视图见长 |
| `rrule` | 通用 | 2.7.1 | 重复规则（每周一、每月末），日程系统的必备件 |
| `cron-parser` | 通用 | — | 解析 cron 表达式，定时任务配置界面用 |
| `cron-expression-validator` | 通用 | — | 校验 cron 表达式是否合法 |
| `solarlunar` | 通用 | — | 农历转换（节假日、生肖、节气） |

### 十二、文件、图片、打印与二维码

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `xlsx` | 通用 | 0.18.5 | 读写 Excel，注意新版本已迁出 npm 官方仓库 |
| `@grapecity/spread-sheets` | 通用 | 16.2.2 | 在线 Excel 编辑器（类飞书表格），功能最全但需商业授权 |
| `js-export-excel` | 通用 | 1.1.4 | 前端简单导出 Excel |
| `file-saver` | 通用 | 2.0.5 | 浏览器端保存文件到本地 |
| `html2canvas` | 通用 | 1.4.1 | DOM 转图片（截图、分享海报），跨域图片会踩坑 |
| `html-to-image` | 通用 | 1.11.11 | DOM 转图片，基于 SVG 实现，比 html2canvas 更现代 |
| `jspdf` | 通用 | 2.5.2 | 前端导出 PDF |
| `react-to-print` | React | — | React 的打印封装，Vue 可用 `vue-print-nb` 或直接调 `window.print()` |
| `qrcode` | 通用 | 1.5.0 | 二维码生成，框架无关 |
| `qrcode.react` | React | — | `qrcode` 的 React 组件封装 |
| `jsbarcode` | 通用 | — | 条形码生成，框架无关 |
| `react-easy-crop` | React | 4.7.4 | 头像裁剪，手势体验好 |
| `react-cropper` | React | — | 头像裁剪，基于 Cropper.js，功能更全 |
| `exif-js` | 通用 | — | 读取图片 EXIF 方向信息（手机拍照会横躺） |
| `image-size` | 通用 | — | 获取图片宽高 |

### 十三、工具函数

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `lodash` | 通用 | 4.17.23 | 防抖节流、深拷贝、对象路径操作 |
| `lodash-es` | 通用 | — | `lodash` 的 ES module 版本，支持 tree-shaking |
| `classnames` | 通用 | 2.5.1 | 条件拼 className，写组件必装；Vue 3 内置对象语法可替代它 |
| `react-classnames` | React | — | 早期的 React 版 className 拼接工具，新项目直接用 `classnames` |
| `uuid` | 通用 | 9.0.1 | 生成唯一 ID |
| `nanoid` | 通用 | 3.3.11 | 更轻量的唯一 ID 库，体积只有几百字节 |
| `crypto-js` | 通用 | 4.1.1 | 对称加解密与哈希，密码传输与签名常用 |
| `jsencrypt` | 通用 | 3.0.0-rc.1 | RSA 加解密 |
| `js-base64` | 通用 | 3.6.0 | Base64 编解码 |
| `js-md5` | 通用 | — | 计算字符串的 MD5 |
| `spark-md5` | 通用 | — | 增量计算 MD5，大文件分片上传时用 |
| `pinyin-pro` | 通用 | 3.13.1 | 汉字转拼音，通讯录分组、搜索联想用 |
| `change-case` | 通用 | — | 字符串大小写风格转换 |
| `omit.js` | 通用 | — | 从对象中剔除指定字段 |
| `flat` | 通用 | — | 对象扁平化与反扁平化 |
| `is-url` | 通用 | — | 判断字符串是否为合法 URL |
| `lz-string` | 通用 | — | 字符串压缩后存 localStorage，避免超配额 |
| `clipboard` | 通用 | 2.0.11 | 一键复制，基于 `document.execCommand` |
| `copy-to-clipboard` | 通用 | — | 一键复制，更轻量的实现 |
| `ahooks` | React | 3.9.6 | 阿里出品的常用 hooks 合集，省掉大量样板代码（分页、防抖、监听、请求） |
| `react-use` | React | 17.4.2 | 社区 hooks 合集，覆盖面广 |
| `@vueuse/core` | Vue | — | 200+ 组合式工具函数（剪贴板、本地存储、防抖节流、暗色模式、传感器等），Vue 3 事实标配，Tree-shaking 友好 |
| `vue-hooks-plus` | Vue | — | 中后台请求向 Hooks，`useRequest` 内置防抖/轮询/重试/SWR 缓存/分页，另带 40+ 常用 hook |
| `ahooks-vue` | Vue | — | 阿里 `ahooks` 的 Vue 3 移植，React 转 Vue 几乎零学习成本 |
| `pubsub-js` | 通用 | — | 跨组件事件总线（发布订阅） |
| `@evecalm/message-hub` | 通用 | — | 消息中心，微前端里做应用间通信 |

### 十四、动画与交互反馈

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `@react-spring/web` | React | 10.0.3 | 物理弹簧动画，过渡自然，React 生态首选 |
| `react-motion` | React | 0.5.2 | 老牌动画库，维护停滞，新项目不建议 |
| `react-transition-group` | React | 4.4.5 | 组件的入场/离场过渡动画，配合 CSS 使用 |
| `animate.css` | 通用 | 3.7.2 | 现成的 CSS 动画类，加个 class 就能用 |
| `nprogress` | 通用 | 0.2.0 | 顶部进度条，路由切换时用 |
| `react-countup` | React | — | 数字滚动动画，数据大屏常见 |
| `typewriter-effect` | React | — | 打字机效果 |
| `intro.js-react` | React | — | 新手引导 / 功能高亮；内核 `intro.js` 框架无关 |
| `@vueuse/motion` | Vue | — | 声明式动画库，指令式写法给元素加入场/过渡动画 |

### 十五、移动端、地图与外部 SDK

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `swiper` | 通用 | 8.4.5 | 轮播，手感好且自带 Vue / React 组件 |
| `react-slick` | React | 0.31.0 | 更贴合 React 写法的轮播组件 |
| `slick-carousel` | 通用 | — | `react-slick` 依赖的底层轮播库 |
| `pullltorefreshjs` | 通用 | — | H5 下拉刷新 |
| `inobounce` | 通用 | — | 禁用 iOS 橡皮筋回弹 |
| `@amap/amap-jsapi-loader` | 通用 | 1.0.1 | 高德地图加载器，只是脚本 loader，跟框架无关 |
| `react-bmapgl` | React | — | 百度地图的 React 封装 |
| `dingtalk-jsapi` | 通用 | — | 钉钉 JSAPI，企业内嵌应用调用原生能力用 |
| `weixin-js-sdk` | 通用 | — | 微信 JS-SDK，公众号 / H5 调用微信能力用 |
| `wps-jsapi` | 通用 | — | WPS JSAPI，WPS 内嵌应用用 |
| `gdt-jsapi` | 通用 | — | 广点通 JSAPI，投放落地页场景用 |

### 十六、国际化

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `react-i18next` | React | 12.1.5 | React 国际化方案，生态最完整，支持命名空间与按需加载语言包 |
| `i18next` | 通用 | 22.4.9 | 国际化内核，框架无关，`react-i18next` 基于它 |
| `react-intl` | React | 2.7.2 | ICU 消息语法规范，配合 `babel-plugin-react-intl` 做文案抽取 |
| `vue-i18n` | Vue | — | Vue 国际化（多语言切换）方案，Vue 3 用 v9 |

### 十七、工程化与质量

| 库 | 框架 | 参考版本 | 说明 |
| --- | --- | --- | --- |
| `webpack` | 通用 | 5.94.0 | 模块打包，`Module Federation` 是 v5 新增能力 |
| `@swc/core` | 通用 | 1.7.40 | 用 Rust 实现的编译内核，换掉 babel 后构建速度提升明显 |
| `swc-loader` | 通用 | — | `@swc/core` 的 webpack loader |
| `sass` | 通用 | 1.43.5 | CSS 预处理器，现用 dart-sass（node-sass 已废弃） |
| `less` | 通用 | 3.9.0 | CSS 预处理器，语法更接近 CSS |
| `typescript` | 通用 | 5.8.2 | 类型系统 |
| `eslint` | 通用 | 7.32.0 | 代码规范检查，v9 起换成 `eslint.config.js` 扁平配置 |
| `@typescript-eslint/*` | 通用 | — | ESLint 的 TypeScript 规则插件，Vue 项目另加 `eslint-plugin-vue` |
| `husky` | 通用 | 9.1.7 | Git hooks 管理，挂载提交前检查 |
| `lint-staged` | 通用 | — | 只对暂存区文件跑 lint |
| `@commitlint/cli` | 通用 | — | 校验提交信息格式规范 |
| `vconsole` | 通用 | 3.15.1 | 移动端真机上的控制台，排查线上问题神器 |
| `web-vitals` | 通用 | — | 采集 LCP、CLS 等真实用户性能指标 |
| `webpack-bundle-analyzer` | 通用 | — | 打包体积分析，找「谁把包撑大了」 |
| `vue` | Vue | 3.x | Vue 3 核心；Vue 2 已于 2023 年底停止维护，新项目一律 Vue 3 |
| `vite` | 通用 | — | Vue 官方推荐的构建工具，开发服务器与打包，速度快 |
| `nuxt` | Vue | — | Vue 全栈框架，SSR / SSG、文件路由、自动导入，官网与 SEO 场景首选 |
| `vitepress` | Vue | — | 基于 Vite 的静态站点生成器，搭文档站/博客首选 |
| `vitest` | 通用 | — | 与 Vite 共用配置的单元测试框架，兼容 Jest API，Vue 项目测试默认选它 |
| `@vue/test-utils` | Vue | — | Vue 官方组件测试库，配合 Vitest 做组件单测 |
| `create-vue` | Vue | — | Vue 官方脚手架，快速生成 Vite + Vue 3 + TS 项目骨架 |
| `unplugin-vue-components` | Vue | — | 组件按需自动导入，模板里直接用组件名、免手写 import 与注册 |
| `unplugin-auto-import` | Vue | — | API 自动导入，`ref` / `computed` / `useRouter` 等免 import，常与上一个搭配 |
| `vite-plugin-vue-devtools` | Vue | — | 以 Vite 插件形式提供 Vue DevTools，免装浏览器扩展，含组件树与时间旅行调试 |

---

## 几条选型经验

1. **同类的库只留一个**。见过一个项目同时引了 `echarts`、`g2plot`、`@ant-design/charts`，三个图表库加起来 2MB+，而业务上只需要一个柱状图。选一个主力，其余特殊情况单独按需加载。

2. **日期库跟 UI 库对齐**。antd v5 之后默认 dayjs，再引 moment 就等于白送 70KB，而且两个库的 `locale` 机制完全不同，切换语言时会发现一半组件不生效。

3. **表格超过 1000 行就别硬渲染**。antd Table 默认全量渲染 DOM，几千行直接卡死。要么开 `virtual`（v5 内置），要么换 `ali-react-table`。

4. **拖拽优先 dnd-kit**。`react-dnd` 最后一次实质更新很早，HTML5 后端在触屏上坑很多；`dnd-kit` 有键盘支持和无障碍，API 也更符合现代 React 习惯。Vue 项目里 `vuedraggable`（`sortablejs`）依旧是性价比最高的选择。

5. **SSE 别手写 EventSource**。原生 `EventSource` 不支持自定义 header（想带 token 只能塞 query），断线重连逻辑也要自己写。`@microsoft/fetch-event-source` 两个问题都解决了。

6. **组件库只该有一个实例**。尤其在微前端里：基座通过 `externals` 把 React 和 antd 外置，微应用不再打包自己的副本，否则页面上会同时存在两套 antd 的 CSS-in-JS 上下文，弹窗层级、主题变量都会错乱。Vue 侧同理，`vue` 本身必须外置，否则 `inject`、响应式系统全断。

7. **体积先看分析器再优化**。`webpack-bundle-analyzer` 跑一次，经常能发现「整个 lodash 被引进来了」或「某个图表库被三个页面重复打包」，改一行 import 就能省几百 KB。

8. **React / Vue 的选型差异集中在三处**：UI 库（`antd` ↔ `element-plus` / `naive-ui`、移动端 `antd-mobile` ↔ `vant`）、状态管理（`zustand` / Redux Toolkit ↔ `pinia` / `vuex`）、副作用与逻辑复用（自定义 hooks ↔ VueUse + Composable）。这几项定下来，其余依赖（`axios`、`dayjs`、`lodash`、`echarts`、`d3`…）两个框架完全可以共用同一份清单，不必重复选型。
