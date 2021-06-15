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
