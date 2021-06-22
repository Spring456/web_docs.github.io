## 关于JS的一些小知识

### 获取URL参数

```
const q = {};
location.search.replace(/([^?&=]+)=([^&]+)/g,(_,k,v)=>q[k]=v);
console.log(q);
```

### ios手机容器滚动条滑动不顺畅

```
overflow: auto;
-webkit-overflow-scrolling: touch;
```

### h5底部输入框被键盘遮挡问题

如果你遇到h5页面这个问题，当输入框在最底部，点击软键盘后输入框会被遮挡，可以如下解决问题：

```
var getHeight = $(document).height();

$(window).resize(function(){
 if($(document).height() < getHeight) {
  $('#footer').css('position','static');
 }else {
  $('#footer').css('position','absolute');
 }
});
```

### 触屏即播放
```
$('html').one('touchstart',function(){
 audio.play()
})
```
