### webpack是什么？

Webpack是一种前端资源构建工具，一个静态模块打包器。

在Webpack看来，前端的所有资源文件(js/json/css/img/less/…)都是模块，只是处理的方式不同。当Webpack处理应用程序时，它将根据模块的依赖关系进行静态分析，打包生成对应的静态资源

### webpack为什么会出现，解决了什么问题？

js是浏览器的脚本语言，但js并没有提供python和java的package包、子模块的import等语法。前端还需要处理html,css等非js文件。

webapck把所有的文件都当作模块来处理，不用纠结这个文件是什么，只是调用配置文件中对模块处理的方式来完成打包的工作。

webpack提供对应的api接口，需要处理哪方面的文件按照规范操作即可。具体呈现在：

**语法转换**：less/sass转换成css、ES6转换成ES5、typescript转换成js

**压缩合并**：html/css/js/图片的压缩与合并（打包）。webpack中支持AMD、CMD、commonJS、ES6模块化等语法

**提供开发环境**：自动开启浏览器、自动监视文件变化、自动刷新浏览器

### webpack的核心概念

**1、Entry**
入口(Entry)表示Webpack以哪个文件作为入口起点分析构建内部依赖图并进行打包。

**2、Output**
输出(Output)表示Webpack打包后的资源bundles输出到哪里去，以及如何命名。

**3、Loader**
Loader让Webpack能够去处理那些非JavaScript语言的文件，比如html,css等。Webpack本身只能理解JavaScript

**4、Plugins**
插件(Plugins)可以用于执行范围更广的任务，插件的范围包括从打包和压缩，一直到重新定义环境中的变量等。

**5、Mode**
模式(Mode)指示Webpack使用相应模式的配置。分为development和production两种模式，下面分别进行简述。

### 两种模式

**development: 开发模式**
能让代码本地运行的环境，会将process.env.NODE_ENV的值设为development，同时启用NamedChunksPlugin和NamedModulesPlugin插件；开发模式不会压缩代码

**production: 生产模式**
能让代码优化运行的环境，会将process.env.NODE_ENV的值设为production，同时启用FlagDependencyUsagePlugin、FlagIncludedChunksPlugin、ModuleConcatenationPlugin、NoEmitreplaceStringsPlugin、OccurrenceOrderPlugin、SideEffectsFlagPlugin和UglifyJsPlugin插件。生产环境会压缩代码。


### webpack其他配置型介绍

**1、context**
context 是webpack entry的上下文，是入口文件所处的目录的绝对路径。默认情况下，是当前项目根目录

**2、module**
