---
title: "Daily"
date: 2020-09-21T20:47:21+08:00
draft: false
---

记录每天零散的xxxxxxx
## 2020/9/21
### TS readonly与const区别
- 两者都是防止re-assign
- readonly是编译时检查
- const是在运行时检查
### Object.freeze()
- `原型链`不能修改
- 不能新增、删除已有属性；不能修改已有属性的可枚举、可配置性、可写、以及不能修改已有属性的值；
## 2020/9/23-2020/9/25
1. 看了关于tree-shaking的内容；连着带出前端m模块化问题import；
2. import问题带出js静态分析、编译问题，解释器、编译器；
3. tree-shaking的sideEffect问题，带出【按需加载】；
4. 按需加载带出了antd提供的, babel-plugin-import的原理；
5. 带出了babel的三个主要步骤，插件编写，AST的理解；
