
---
title: "Webpack"
date: 2020-09-21T21:16:21+08:00
draft: false
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

# 简易实现
## 定义Compiler

```javascript
   class Compiler {
        constructor(option) {
            const { entry, output } = option;
            this.entry = entry;
            this.output = output;
            this.module = [];
        }
        run() {
        	const ast = Parse.parse(this.entry)
          const dependencies = Parse.getDependencies(ast, this.entry);
          const code = Parse.getCode(ast);
        }
        //重写require函数，输出bundle
        generate() {
        
        }           
    }
```
## 解析入口文件获取AST

使用`babel/parser`实现分析内部的语法，包括ES6、返回AST；
```javascript
const parser = require('@babel/parser');
const Parser = {
	getAst: (path) => {
    const content = fs.readFileSync(path,'utf-8');
    return parser.parse(content, {
      sourceType: 'module',
    })
  }
}                
```

## 找出所有模块依赖

````javascript
const traverse = require('@babel/traverse').default;
const Parser = {
	getAst: () => {},
  getDependencies: (ast, filename) => {
    const dependencies = {};
    traverse(ast, {
      ImportDelaration({ node }) {
				const dirname = path.dirname(filename);
        const filepath = './' + path.join(dirname, node.source.value);
        dependencies[node.source.value] = filepath;
			}
		})
		return dependencies; 
  }
} 
````



## AST转译

讲AST转化成浏览器可执行的代码，使用`babel/core`和`babel/preset-env`

```javascript
const { transformFromAst } = require('@babel/core');
const Parser = {
	getAst: () => {},
  getDependencies: (ast, filename) => {},
  getCode: ast => {
  	const { core } = transformFromAst(ast, null, {
      parests: ['@babel/preset-env']
    })
    return core
  }
} 
```



## 递归解析生成依赖关系

## 重写加载函数输出bundle

# 源码分析



