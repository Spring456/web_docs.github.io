## 模块化开发的演变过程

### 早期

1、文件划分方式

缺点：污染全局作用域、命名冲突、无法管理模块依赖关系、早期模块化完全依靠约定

2、命名空间方式

缺点：模块成员可以被修改

3、IIFE，使用立即执行函数表达式

缺点：无法处理模块之间的依赖关系

### 模块化规范的出现

`**CommonJS 规范**`

1、一个文件就是一个模块

2、每个模块都有单独的作用域

3、通过 module.exports 导出成员

4、通过 require 函数载入模块

5、是以同步模式加载模块

`**AMD(Asynchronous Module Definition), require.js**`

define 函数，定义一个模块

require 函数，载入一个模块

目前绝大多数第三方库都支持 AMD 规范

AMD 使用起来相对复杂

模块 JS 文件请求频繁

`**Sea.js + CMD(Common Module Definition)**`

Sea.js，淘宝团队推出的库，类似 CommonJS 规范

使用上有点类似 require.js

### 模块化标准规范

1、node.js 环境中，遵循 CommonJS 规范

2、浏览器环境中，遵循 ES Modules 规范

