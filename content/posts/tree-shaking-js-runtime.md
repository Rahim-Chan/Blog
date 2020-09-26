---
title: "Tree-Shaking牵扯到JS编译时"
date: 2020-09-24T11:17:17+08:00
draft: false
---

最近在“复习”Webpack原理分析中，遇到了**Tree-Shaking**，想必`tree-shaking`这个概念大家都耳濡目染了。但是别人问我它具体是怎么实现的，“就都抖嘛！抖掉不要的代码就是了”垮拉着个P脸，完美做到了“耳熟”不“能详”。接下来从以下几个切入点捋一捋！！

## 切入点

`Why`-`Which`-How ==>`为啥要做 `-`谁可以做`-`怎么做`

让我们从以上几个切入点，逐层分析，揭开Tree-Shaking神秘的面纱吧！！

## Why-为啥要使用Tree-Shaking

**TreeShaking** 是 DCE（Dead Code Elimination）的实现，该功能让代码文件中没有使用过的代码片段能在构建过程中删除，从而达到构建产物的优化。

基于 ES6 的静态引用，TreeShaking 通过扫描所有 ES6 的 `export`，找出被 `import` 的内容并添加到最终代码中。

引用 TreeShaking 提出者也是 Rollup 作者的比喻：

> 如果把代码打包比作制作蛋糕。传统的方式是把鸡蛋（带壳）全部丢进去搅拌，然后放入烤箱，最后把（没有用的）蛋壳全部挑选并剔除出去。而 TreeShaking 则是一开始就把有用的蛋白蛋黄放入搅拌，最后直接作出蛋糕。

## Who-哪个工具能够开启

- **Rollup**是默认开启，至于怎么[关掉](https://github.com/rollup/rollup/issues/505)；
- Webpack
  1. 根据 Webpack 官网的提示，Webpack2 支持 TreeShaking，需要修改配置文件，指定 Babel 处理 JavaScript 文件时不要将 ES6 模块转成 CommonJS 模块，具体做法就是：在 `.babelrc` 设置 `babel-preset-es2015` 的 `modules` 为 `fasle`，**表示不对 ES6 模块进行处理**。
  2. Webpack4默认配置继承了，不过要在`mode=production`中，其他模式下[自行配置](https://segmentfault.com/a/1190000016767989)（不赘述了）。

无论rollup抑或是webpack都是通过Uglify（压缩工具）这个库把实现DCE抖掉！！

## How-具体流程是怎么样

### import入口静态分析

- JS里面没有编译的动作，可以理解成**解析时**，相对于运行时；

- `import { readFile } from 'fs';`

  1. 当js引擎编译时，将上面的fs模块的readFile指向对应模块的export cont readFile() 方法上（这里只是做了指针指引）编译时任务到此；
  2. 当执行readFile()时，就会去找指针指向的代码执行；

- `let { stat, exists, readFile } = require('fs');`

  1. 代码先执行require('fs')模块，得到一份拷贝的代码；

     ```javascript
     let fs = require('fs');
     let stat = _fs.stat;
     let exists = _fs.exists;
     let readfile = _fs.readfile;
     ```

总结： 因此import只是做了指针引用对应的属性和方法，所以无法处理带有计算的import;而require时执行代码获取属性和方法，所以可以动态；

### 删除无用代码

实现细节（简单带过）

- 所有`import`标记为`/* harmony import*/`
- 被使用过的`export`标记为`/* harmony export ([type])*/`，其中[type]和webpack内部有关，可能是`binding`、`immutable`
- 未被使用过的`export`标记为` /* unused harmony exprot [FuncName] */`,其中[FuncName]为export的方法名称
- 之后在uglifyjs（或者其他工具）步骤进行代码精简；

### 开启（副作用不能识别）

- 尽量不要编写有副作用的代码，诸如写了立即执行函数，引用了外部的变量；
- 对ES6语意不严格，可以开启**loose**模式；根据自身项目判断，如真的要不可枚举class对属性（class内部属性不可枚举）；
- 将功能函数或组件，打包成单独的文件或者目录，以便于用户根据目录去加载；如有条件，可以为自己的库开发webpack-loader,便于用户按需加载； 
- `pure_getters:true`删除一些强制认为不会产生副作用的代码；
- 导出module入口；使用必须开启第三方node_module编译
- Closure Compiler （入侵性强）

### 识别代码

讲了（CV）这么多。一切的`DCE`、`side-effects`的过滤结果都来得这么自然；那问题来了那它怎么被**识别**出来的？

https://github.com/webpack/webpack/blob/master/lib/FlagDependencyUsagePlugin.js











