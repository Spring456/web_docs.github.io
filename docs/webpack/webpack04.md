## webpack打包原理和流程是怎样的？

### webpack构建流程

Webpack 的运行流程是一个串行的过程,从启动到结束会依次执行以下流程 :

1、初始化参数：生成options (将webpack.config.js和shell中的参数，合并中options对象)

2、开始编译：实例化complier对象 （webpack全局的配置对象，包含entry，output，loader，plugins等所有配置信息）

3、实例化Compilation对象：compiler.run方法执行，开始编译过程，生成Compilation对象

4、编译模块：分析入口js文件，调用AST引擎(acorn)处理入口文件，生成抽象语法树AST，根据AST构建模块的所有依赖

5、完成模块编译：通过loader处理入口文件的所有依赖，转换为js模块，生成AST，继续遍历，构建依赖的依赖，递归，直至所有依赖分析完毕

6、输出资源：对生成的所有module进行处理，调用plugins，合并，拆分，生成chunk

7、输出完成：将chunk生成为对应bundle文件，输出到目录

在以上过程中,Webpack 会在特定的时间点广播出特定的事件,插件在监听到感兴趣的事件后会执行特定的逻辑,并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。

### 实现简易的webpack

1. 定义 Compiler 类

2. 解析入口文件,获取 AST

使用@babel/parser,这是 babel7 的工具,来帮助我们分析内部的语法,包括 es6,返回一个 AST 抽象语法树。

3. 找出所有依赖模块

Babel 提供了@babel/traverse(遍历)方法维护这 AST 树的整体状态,我们这里使用它来帮我们找出依赖模块。

4. AST 转换为 code

将 AST 语法树转换为浏览器可执行代码,我们这里使用@babel/core 和 @babel/preset-env。

5. 递归解析所有依赖项,生成依赖关系图

6. 重写 require 函数,输出 bundle


文章参考：https://juejin.cn/post/6844904038543130637