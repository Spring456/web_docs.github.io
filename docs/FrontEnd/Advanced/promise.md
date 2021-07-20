## 详解promise

### 1、什么是promise

>Promise是JS异步编程中的重要概念，异步抽象处理对象，是目前比较流行Javascript异步编程解决方案之一

### 2、promise解决了什么问题

要想知道promise解决了什么问题，我们首先来看下，如果不使用promise，我们在处理请求，浏览器事件时，是如何操作的。

了解浏览器事件执行，我们需要知道JS中的同步任务和异步任务。

同步任务：在主线程上排队执行的任务，只有当前一个任务执行完毕，才能执行下一个任务。

异步任务：事件不用进入主线程，而是进入'任务队列'，只有'任务队列'通知主线程，某个异步任务可以执行了，该任务才可以进入主线程执行。

更具体的可以看看事件循环机制：[事件循环机制](FrontEnd/Advanced/closure.md)

js中最常见的异步处理事件，就是定时器和ajax

```
function callback() {
    console.log('定时器');
}
console.log('开始执行');
setTimeout(callback, 1000); // 1秒钟后调用callback函数
console.log('结束执行');
```
结果：
```
开始执行
结束执行
(等待1秒后)
定时器
```
我们可以看到，setTimeout改变了事件执行顺序，因此setTimeout是异步操作。ajax默认的就是异步操作

```
request.onreadystatechange = function () {
    if (request.readyState === 4) {
        if (request.status === 200) {
            return success(request.responseText);
        } else {
            return fail(request.status);
        }
    }
}
```
当请求较多或者下一个请求是依赖上一个请求的返回值的时候，这个时候就需要频繁的使用ajax，或者ajax里面嵌套使用ajax，陷入'回调地域'的地步，实现起来并不友好也不方便。

比如在使用jquery的ajax获取数据时，都是以回调函数方式获取的数据.

```
$.get(url, (data) => {
    console.log(data)
)
```

当我们需要发送多个异步请求 并且每个请求之间需要相互依赖 那这时 我们只能 以嵌套方式来解决 形成 "回调地狱"

```
$.get(url, data1 => {
    console.log(data1)
    $.get(data1.url, data2 => {
        console.log(data1)
    })
})
```
这就会带来一些问题：

a、代码逻辑书写顺序与执行顺序不一致，不利于阅读与维护。

b、异步操作的顺序变更时，需要大规模的代码重构。

c、回调函数基本都是匿名函数，bug 追踪困难。

因此promise对象就是为了解决这个问题。

总结：promise有以下优点

a、异步操作，解决回调地狱的问题

b、异步操作队列化，按照期望的顺序执行，返回期望的结果

c、在对象之间传递和操作promise，帮助我们更好的处理队列问题，接口数据传递问题

### 3、promise处理异步请求

`promise的创建`
```
var promise = new Promise(function(resolve, reject) {
    // 异步处理
    // 处理结束后、调用resolve 或 reject
});
```

从上面的构造函数可以看出：promise构造函数包含了两个参数的回调。一个是解析成功的回调——resolve()，一个是解析失败的回调——reject()

resolve 方法和 reject 方法调用时，都带有参数。它们的参数会被传递给回调函数。reject 方法的参数通常是 Error 对象的实例，而 resolve 方法的参数除了正常的值以外，还可能是另一个 Promise实例

```
const request = url => { 
    return new Promise((resolve, reject) => {//创建promise
        $.get((url, data)) => {
            if(data.status==200){
                resolve(data)//请求成功后调用resolve
            }else{
                reject(data)//请求失败调用reject
            }
            
        });
    })
};

// 使用实例调用then()和catch()方法
request(url).then(data1 => {
    return request(data1.url);   
}).then(data2 => {
    return request(data2.url);
}).then(data3 => {
    console.log(data3);
}).catch(err => throw new Error(err));
```

其实例化对象可以调用then()和catch()方法，传递的是resolve()和reject()方法的值。


`promise的状态`

promise的三种状态:pending、fullfilled、rejected

1、promise对象初始化状态为 pending

2、当调用resolve(成功)，会由pending => fullfilled。

3、当调用reject(失败)，会由pending => rejected

promise对象的状态只能由pending变为fulfilled或rejected，不可逆。

如果在promise内部不写resolve或reject，相当于没有回调函数，则promise就不会执行下面的then方法，其状态就会一直是pending状态。

```
const b = new Promise((resolve, reject) => {
  console.log('promise1');//能打印出来promise1
  // resolve();
}).then(() => {
  console.log('promise2');//上面注释了resolve，相当于没有回调返回，这个不打印
});
```

`promise的方法`

`**1、then()方法**`

**a、promise的then()方法返回的是是一个新的promise对象，因此可以使用链式调用。**

```
request(url).then(data1 => {
    return request(data1.url);   
}).then(data2 => {
    return request(data2.url);
}).then(data3 => {
    console.log(data3);
}).catch(err => throw new Error(err));
```

**b、then()既然返回的是promise对象，那就是异步的操作**

```
let promise = new Promise((resolve,reject)=>{
    console.log('我是同步的')//率先执行
    resolve()
}).then(()=>{
    console.log('我是异步的')//最后执行
})
console.log('我也是同步的')//第二步就执行
```

`**catch()方法**`

catch()方法用于指定发生错误时的回调函数。

Promise 对象的错误具有"冒泡"性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个 catch 语句捕获。

`**Promise.all方法，Promise.race方法**`

Promise.all() 方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

```
var p = Promise.all([p1,p2,p3]);
```

Promise.all()方法接受一个数组作为参数，返回的都是Promise的实例。

返回状态由其参数决定，

（1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。

（2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。


Promise.race 方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。

```
var p = Promise.race([p1,p2,p3]);
```

上面代码中，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的Promise实例的返回值，就传递给p的返回值。

如果Promise.all方法和Promise.race方法的参数，不是Promise实例，就会先调用下面讲到的Promise.resolve方法，将参数转为Promise实例，再进一步处理。

`**Promise.resolve 方法，Promise.reject 方法**`

Promise.resolve方法,将现有对象转为Promise对象

如果 Promise.resolve 方法的参数，不是具有 then 方法的对象（又称 thenable 对象），则返回一个新的 Promise 对象，且它的状态为fulfilled。

```
var p = Promise.resolve('Hello');
p.then(function (s){
  console.log(s)
});
// Hello
```

上面代码生成一个新的Promise对象的实例p，它的状态为fulfilled，所以回调函数会立即执行，Promise.resolve方法的参数就是回调函数的参数。

如果Promise.resolve方法的参数是一个Promise对象的实例，则会被原封不动地返回。

Promise.reject(reason)方法也会返回一个新的Promise实例，该实例的状态为rejected。Promise.reject方法的参数reason，会被传递给实例的回调函数。

```
var p = Promise.reject('出错了');
 
p.then(null, function (s){
  console.log(s)
});
// 出错了
```
上面代码生成一个Promise对象的实例，状态为rejected，回调函数会立即执行。


### 4、promise的参数，运行等的理解

**1、promise是同步任务，但promise的回调函数（then()方法里）是异步任务，而且是微任务**

详细请看事件循环机制：[事件循环机制](FrontEnd/Advanced/closure.md)

```
console.log(1);

setTimeout(() => {
    console.log(2);
}, 10);

new Promise(function(resolve,reject){
    console.log(3);
    resolve('');
    console.log(4);
}).then(res=>{
    console.log(5);
})
console.log(6);
})

//执行顺序：1 3 4 6 5 2
  
```
执行顺序：1 3 4 6 5 2 ,先执行同步任务，再执行异步任务，先执行异步任务里面的微任务，最后执行异步任务里的宏任务。

**2、then 和 catch 期望接收函数做参数，如果非函数就会发生 Promise 穿透现象，打印的是上一个 Promise 的返回。**

```
var promise = new Promise(function(resolve, reject){
  setTimeout(function() {
    resolve(1);
  })
})

promise.then(2).then((n) => {
  console.log(n)//1
});
```

**3、then()返回的回调函数会作为下一个then()事件的形参**

```
var promise = new Promise(function(resolve, reject){
  setTimeout(function() {
    resolve(1);
  })
})

promise.then(() => {
  return 2
}).then((n) => {
  console.log(n)//2
});
```

**4、Promise.resolve()返回的是新的promise对象，然后执行异步事件**

```
var promise = new Promise(function(resolve, reject){
  setTimeout(function() {
    resolve(1);
  })
})

promise.then(() => {
  return Promise.resolve(3);
}).then((n) => {
  console.log(n)//3
});
```

**5、promise里嵌套promise，需等待里面的promise全部执行完毕且resolve()或reject()完毕之后才执行**

```
let a;
const b = new Promise((resolve, reject) => {
    console.log('promise1');
    resolve();
}).then(() => {
    console.log('promise2');
})

a = new Promise(async (resolve, reject) => {
    console.log(a);//undefined,原因是a只是定义但未赋值
    await b;
    console.log(12);
});
<!-- 结果为promise1、undefined、promise2、12 -->
```

**6、Promise 对象的状态只能被转移一次，只能从pending赚为fullfiled或rejected,不可逆**

```
const promise = new Promise((resolve, reject) => {
  resolve('success1');
  reject('error');
  resolve('success2');
});

promise
  .then((res) => {
    console.log(res);//只打印success1，后面的都不执行
  })
  .catch((err) => {
    console.log(err);
  });
```

**7、promise的缺陷在于不能取消，只能等resolve或reject执行或抛错。**


