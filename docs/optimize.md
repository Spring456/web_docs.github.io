## 项目整体层面

1.减少HTTP请求
* 减少请求次数 -- 资源合并
* 减少单次请求所花费的时间 -- 资源压缩

这两个方面主要交给构建工具webpack来完成，可以通过webpack工具或插件来实现

2.Gzip压缩

3.静态资源放置在cdn

4.利用页面数据存储

5.图片处理

## 页面层面
1.减少对DOM结构的操作

2.避免回流重绘
将上述的会触发回流的属性缓存起来，避免不必要的重复触发
避免逐条改动样式，最好通过class一次性改动
有复杂改动时将DOM离线，即将DOM先隐藏，复杂改变结束后再显示
需要频繁变更的元素使用脱离文档流的样式

3.节流防抖

4.长列表函数冻结

## vue项目相关
1.路由使用懒加载

2.插件按需引入

3.组件细分，组件异步加载(按需加载)

## 用户角度
1.加载中的提示