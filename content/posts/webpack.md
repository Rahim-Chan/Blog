
---
title: "Webpack"
date: 2020-09-21T21:16:21+08:00
draft: true
---
参考：[Webpack Guidebook](https://tsejx.github.io/webpack-guidebook/principle-analysis/implementation-principle/workflow)

# 构建过程

Webpack的运行流程是一个串行的过程：
1. 初始化参数： 配置文件 & 命令行；                                                   
2. 开始编译：通过配置参数初始化Compiler对象，加载`所有配置的插件`，执行对象的`run`方法开始执行编译；
3. 确定入口：entry属性确定入口；
4. 编译模块：调用所有配置的loader对**模块**进行翻译，再找出模块的依赖，再递归直到所有文件入口依赖都经过了本步骤的处理；
5. 完成模块编译：得到每个模块翻译后以及它们之间的**关系图**；
6. 输出资源：根据文件入口与依赖关系，组装成Chunk，再把Chunk转化成单独的文件加入到列表中，这是最后一个修改内容的机会；
7. 输出完成：确定好**输出内容**后，按照配置和，把文件写入系统；
   