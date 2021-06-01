webpack中的属性，方法更详细参考webpack中文网：https://www.webpackjs.com/concepts/

### webpack指定打包配置文件命令
```
webpack --config webpack.dev.config.js
```
### 配合npm脚本使用命令行的参数
/package.json
![webpack01](../../_media/webpack01.png)
然后在命令行运行 npm run webpack

### webpack核心参数介绍

**1、entry打包入口文件**
为了应付各种需求，官方给出三种方式来匹配不同需求。
第一种: 输入简单string指定一个简单入口文件。所有依赖都在这个一个入口文件指定。
第二种：是一个数组 ，webpack为了解决两个平行互相依赖的文件，却想打包到一起。
第三种：是一个对象分key和value。多页面应用程序会运用这个场景。

**2、output 打包出口**
在 webpack 中配置 output 属性的最低要求是，将它的值设置为一个对象，包括以下两点：
1、filename 用于输出文件的文件名。
2、目标输出目录 path 的绝对路径。

**3、mode模式**
有两种，development和production
**4、loader**
对模块的源代码进行转换。通过指定Presets来告诉babel loader转换某一些特性，如果转换所有特性可以指定latest（es2015,2016,2017）怎么指定插件呢？给loader指定一个参数Query parameters

1、require（'url-loader？'）//？后面跟参数

2、配置文件中
`loaders: [ { test: /\.js$/, loader:'babel', query:{ presets:['lastest'] //指定loader } } ]`

3、还可以在项目根目录建 .babelrc 配置文件
```
babel:{
    "presets":["es2015"]
}
```
4、还可以在package.json文件指定

在配置文件中，module.loaders定义的参数解析
● test——js正则匹配 条件  
● exclude——loader排除范围，比如某一个目录下不让它处理  
● include——loader处理的范围  
● loader——单个loader使用字符串形式
● loaders——把一长串的loader通过一个数组表示

```
{
    test: /\.js$/,//正则匹配js文件
    loader: 'babel-loader',//loader，处理js的loader
    include: './src/',//指定处理的文件夹
    exclude:'./node_modules',//指定不处理的文件
    query:{
        presets:['latest']//指定babel loader转换某一些特性
    }
},
```
还可以使用path来指定处理或不处理的文件。
path是Node的一个api，path对象上有一个方法resolv（解析），dirname是运行环境下的变量，也就是在当前运行环境的路径，后面是相对路径。
（dirname）当前的目录，再加个一个相对路径（api/src），解析完后得到一个绝对路径

```
{
    test: /\.js$/,//正则匹配js文件
    loader: 'babel-loader',//loader，处理js的loader
    include: path.resolve(__dirname,'src'),//__dirname运行环境下的变量，也就是当前运行环境的路径
    exclude:path.resolve(__dirname,'node_modules'),//指定不处理的文件
    query:{
        presets:['latest']//指定babel loader转换某一些特性
    }
},
```
其他常用的loader有，css-loader、style-loader、html-loader、url-loader等

**5、plugins**
由于插件可以携带参数/选项，你必须在 webpack 配置中，向 plugins 属性传入 new 实例。

插件目的在于解决 loader 无法实现的其他事。

插件的使用：
由于插件可以接受参数/选项，因此您必须将new实例传递给pluginswebpack 配置中的属性。

webpack官网提供了若干插件：https://webpack.js.org/plugins/


### plugins和loaders的区别

loader 用于对模块的源代码进行转换。

loader 可以使你在 import 或"加载"模块时预处理文件。

因此，loader 类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。

插件是 webpack 的支柱功能。

webpack 自身也是构建于，你在 webpack 配置中用到的相同的插件系统之上！

插件目的在于解决 loader 无法实现的其他事

**plugin是在loader之后执行的，当loader处理完模块代码，plugin继续处理loader未能做完的事情**

### webpack跨域配置
config->index.js 配置其中的proxyTable
```
proxyTable: {
      '/api': {
        target: 'http://www.baidu.com',//接口域名
        changeOrigin: true,//是否跨域
        pathRewrite: {
          '^/api': '', //地址重写,省略api 
        }
      },
    },
```

使用vue-cli3建立的工程，在webpack.config.js
```
devServer: {
    proxy: [　　　　　　　　　　　// 例如将'localhost: 8080/api/xxx'代理到'http:www.baidu.com/api/xxx
        {
            context: ['/api'],
            target: 'http:www.baidu.com',//接口域名
            changeOrigin: true, //如果是https需要配置该参数
            secure: false, //如果接口跨域需要进行该配置
        },
　　　　　// 例如将'localhost: 8080/img/xxx'代理到'http:www.baidu.com/xxx
        {
            context: ['/img'],
            target: 'http:www.baidu.com',//接口域名
            changeOrigin: true, //如果是https需要配置该参数
            secure: false, //如果接口跨域需要进行该配置
            pathRewrite: {//是指服务器把接口中api去掉，以免img这几个字母加入到接口地址中
                '^/img': ''
            }
        },
] }
```



