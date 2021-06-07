## webpack的loader和plugin有哪些不同？

### Loader 和 Plugin 的不同点：

Loader 专注实现资源模块的转换和加载（编译转换代码、文件操作、代码检查）

Plugin 解决其他自动化工作（打包之前清除 dist 目录、拷贝静态文件、压缩代码等等）。当loader不能实现的就可以使用plugin解决

### loader开发思路

可以直接在项目根目录新建 test-loader.js （完成后也可以发布到 npm 作为独立模块使用）

这个文件需要导出一个函数，这个函数就是我们的 loader 对所加载到的资源的处理过程

函数输入为 加载到的资源，输出为 加工后的结果

输出结果可以有两种形式：第一，输出标准的 JS 代码，让打包结果的代码能正常执行；第二，输出处理结果，交给下一个 loader 进一步处理成 JS 代码

在 webpack.config.js 中使用 loader，配置 module.rules ，其中 use 除了可以使用模块名称，也可以使用模块路径

### 开发plugin思路

plugin 是通过钩子机制实现的，我们可以在不同的事件节点上挂载不同的任务，就可以扩展一个插件

插件必须是一个函数或者是一个包含 apply 方法的对象

一般可以把插件定义为一个类型，在类型中定义一个 apply 方法

apply 方法接收一个 compiler 参数，包含了这次构建的所有配置信息，通过这个对象注册钩子函数

通过 compiler.hooks.emit.tap 注册钩子函数（emit也可以为其他事件），钩子函数第一个参数为插件名称，第二个参数 compilation
 
为此次打包的上下文，根据 compilation.assets 就可以拿到此次打包的资源，做一些相应的逻辑处理

