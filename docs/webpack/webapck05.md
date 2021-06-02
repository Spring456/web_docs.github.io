## webpack热更新原理

### 热更新是什么？

热更新(Hot Module Replacement,简称：HMR),是指当你修改了代码保存后，webpack会自动对代码打包，并将改动的模块发送到浏览器，浏览器用新的模块来替代旧的模块，实现局部刷新而不是整体刷新页面。

### 热更新的优点

热更新相比较于live reload整体刷新页面的方案，HMR的优点在于可以保存应用状态，只改变已经被改变的，能提高开发效率。

### 热更新简单流程

热更新简单流程：

1、webpack监听文件变化，自动打包，保存在内存中。

2、服务端和客户端有websocket通信

3、服务端向客户端发送文件变化消息

4、HotModuleReplacementPlugin根据文件变化消息获取变更模块代码，进行模块代码的热替换

### 热更新相关的名词

webpack-dev-server、webpack-dev-middleware、webpack-dev-server/client、HMR.runtime、webpack/hot/dev-server

**1、webpack-dev-server**

一个基于node.js和webpack的简易服务器，限开发时使用。WDS包含三个核心功能，分别是提供一个简易的web服务器、webpack-dev-middleware以及webpack-dev-server/client。

**2、webpack-dev-middleware**

一个中间件，用于与webpack的compiler对象绑定，在dev-server启动时会调用，限开发使用。有以下三个特性：

通过watch mode，监听资源变更，然后自动打包。

打包后的文件存入内存中，而不是磁盘。

支持HMR

**3、webpack-dev-server/client**

与服务端进行一个websocket连接，并对服务端的消息作出响应及消息传递。

**4、HMR.runtime**

HotModuleReplacementPlugin插件提供，用于在浏览器端请求更新后的资源以及进行热替换操作。

**5、webpack/hot/dev-server**

根据webpack-dev-server/client传递的消息，决定是进行live reload浏览器刷新还是热更新。

### 热更新详细流程

1、配置开启热更新，设置entry格式和webpack-dev-server的option，使得打包的bundle里面包含HMR runtime和websocket连接的代码

2、webpack-dev-server通过express启动服务端

3、客户端通过sockjs和服务端建立websocket长连接

通过WDS里的sockjs在浏览器端和服务器之间建立websocket长连接。这一步的目的是将webpack编译打包的各个阶段状态告知浏览器，让浏览器端可以根据这些消息进行不同的操作，主要传递的还是新模块的hash值

4、在webpack watch模式下更新代码，webpack会自动编译。以webpack-dev-server运行默认watch:true。编译后的代码保存在内存中，不在output.path中产生输出。

由中间件webpack-dev-middleware对webpack暴露的api进行监控并告诉webpack将资源打包到内存中，保存为一个Javascript对象。

5、编译会生成hash值，hot-update.json(已改动模块的json)，hot-update.js(已改动模块的js)
  
6、HMR.runtime它接收到webpack-dev-server/client传递的新模块hash值，然后通过JsonpMainTemplate.runtime向server端发送Ajax请求，请求返回一个json，包含了所有要更新的模块hash值，然后该模块再通过jsonp请求，获取到最新的模块代码。

7、更新模块，HMR会对新旧模块进行对比，决定是否更新模块，决定更新后，检查模块之间的依赖关系，更新模块的同时更新模块间的依赖引用。

8、如果上一步HMR更新失败，则回退到live reload的操作。

### webpack/node.js是如何监听文件变化的？

nodejs中监听文件变化的api为：fs.watch | fs.watchFile

webpack可以通过配置watch开启监听文件变化。

在webpack中，监听一个文件发生变化的原理是定时获取该文件的最后编辑时间，如果发现该时间与上一次保存的时间不一致则认为该文件发生了变化。其中，我们可以在配置项watchOptions.poll中设置轮询的间隔时间。

在发现一个文件发生变动时，并不会马上通知监听者，而是先缓存起来，收集一段时间后，再通知。这段时间的配置可以在watchOptions.aggregateTimeout配置，默认值300ms


### HMR.runtime及webpack-dev-server/client何时注入到浏览器代码（即bundle）中?

HMR.runtime：是由HotModuleReplacementPlugin插入，现在默认在webpack/lib/HotModuleReplacement.runtime中

webpack-dev-server/client：WDS修改了webpack配置中的entry属性，在里面添加了webpack-dev-server/client的代码，这样在最后生成的bundle.js文件中就有了这一部分的代码了。

文章整理自：https://www.jianshu.com/p/4bc89dd91327、https://www.cnblogs.com/mengff/p/12590406.html





