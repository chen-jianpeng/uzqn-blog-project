---
title: vue源码阅读
date: 2018-03-14 14:19:16
tags: vue
header-img: "head.jpg"
---

#vue源码阅读

本文所阅读的代码，时vue的2.5.16版本。

## vue初始化

从package.json文件入手，执行npm run dev的时候，相当于执行了下面的脚本

```
rollup -w -c scripts/config.js --environment TARGET:web-full-dev
```

Rollup 是一个 JavaScript 模块打包器，可以将小块代码编译成大块复杂的代码。
-w : watch
-c : 指定config文件地址
-