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

Loader——文件转换器，将webpack不能处理的模块转换为webpack能处理的模块，就是js模块。还可以处理html，css等模块

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

模块，在 webpack 中，一切皆为模块，一个模块对应一个文件

**3、Chunk**

代码块，一个 chunk 由多个模块组合而成，用于代码的合并与分割。是进行依赖分析的时候，代码分割出来的代码块，包括一个或多个module，是被分组了的module集合，code spliting之后的就是chunk

**4、bundle**

是文件，最终打包出来的文件，通常一个bundle对应一个chunk

### webpack核心对象

**1、Tapable**

控制钩子的发布与订阅，Compiler和Compilation 对象都继承于 Tapable

**2、Compiler**

Compiler 继承 Tapable 对象，可以广播和监听 webpack 事件。

Compiler 对象是 webpack 的编译器，webpack 周期中只存在一个 Compiler 对象。

Compiler 对象在 webpack 启动时创建实例，compiler 实例中包含 webpack 的完整配置，包括 loaders, plugins 信息。

**3、Compilation**

Compilation 继承 Tapable 对象，可以广播和监听 webpack 事件。

Compilation 实例仅代表一次 webpack 构建和生成编译资源的的过程。

webpack 开发模式开启 watch 选项，每次检测到入口文件模块变化时，会创建一次新的编译: 生成一次新的编译资源和新的 compilation 对象，这个 compilation 对象包含了当前编译的模块资源 module, 编译生成的资源，变化的文件, 依赖的的状态