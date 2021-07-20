## 函数防抖和节流

在开发中，我们经常会持续触发一些事件，比如持续点击按钮提交，如窗口的resize、scroll事件，鼠标的mousemove事件等等。这些事件会在连续触发过程中频繁执行，频繁的操作会加大浏览器负担以及接口请求的频次。

所以为了解决这个问题，可以采用防抖节流两种方式解决

### 防抖

概念：

所谓防抖，就是指触发事件后 n 秒后才执行函数，如果在 n 秒内又触发了事件，则会清空之前的定时器，重新生成新的定时器，重新计时。

防抖代码：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>防抖</title>
</head>
<body>
  <button id="debounce">点我防抖！</button>

  <script>
    window.onload = function() {
      // 1、获取这个按钮，并绑定事件
      var myDebounce = document.getElementById("debounce");
      myDebounce.addEventListener("click", debounce(sayDebounce));
    }

    // 2、防抖功能函数，接受传参
    function debounce(fn) {
      // 4、创建一个标记用来存放定时器的返回值
      let timeout = null;
      return function() {
        // 5、每次当用户点击/输入的时候，把前一个定时器清除
        clearTimeout(timeout);
        // 6、然后创建一个新的 setTimeout，
        // 这样就能保证点击按钮后的 interval 间隔内
        // 如果用户还点击了的话，就不会执行 fn 函数
        timeout = setTimeout(() => {
          fn.call(this, arguments);
        }, 1000);
      };
    }

    // 3、需要进行防抖的事件处理
    function sayDebounce() {
      // ... 有些需要防抖的工作，在这里执行
      console.log("防抖成功！");
    }
  </script>
</body>
</html>
```

结合上面代码，我们可以看到当我们连续点击按钮的时候，并不会一直触发事件，直到指定间隔时间1s后才执行。

上面的例子中使用了argumenets,这有什么用呢？

arguments 理解为能实现重载函数功能的工具，比如说有一个函数——function test() ，由于我们不确定test()的参数有多少个，比如 test("jsliang", 24)，又或者 test("LiangJunrong", "jsliang", "24")，这时候只需要在函数 test 中用 arguments 接收就行了。

最后，在 function test() { let arr1 = argument[0] } 中，arr1 就可以获取到传进来的第一个变量。

所以，fn.call(this, arguments) 其实是将不确定变量替换到函数中了。

### 节流

概念：节流是指定间隔时间只执行一次事件。不管点击多少次，事件总是在相同的间隔时间内执行

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>节流</title>
</head>
<body>

  <button id="throttle">点我节流！</button>

  <script>
    window.onload = function() {
      // 1、获取按钮，绑定点击事件
      var myThrottle = document.getElementById("throttle");
      myThrottle.addEventListener("click", throttle(sayThrottle));
    }

    // 2、节流函数体
    function throttle(fn) {
      // 4、通过闭包保存一个标记
      let canRun = true;
      return function() {
        // 5、在函数开头判断标志是否为 true，不为 true 则中断函数
        if(!canRun) {
          return;
        }
        // 6、将 canRun 设置为 false，防止执行之前再被执行
        canRun = false;
        // 7、定时器
        setTimeout( () => {
          fn.call(this, arguments);
          // 8、执行完事件（比如调用完接口）之后，重新将这个标志设置为 true
          canRun = true;
        }, 1000);
      };
    }

    // 3、需要节流的事件
    function sayThrottle() {
      console.log("节流成功！");
    }

  </script>
</body>
</html>
```
节流相比较于防抖，节流使用标识来判断事件执行。


### 防抖节流在实际工作中的应用

防抖：

在input框上输入，然后请求接口。实现这个功能可以使用防抖。避免每次输入就请求接口，使用防抖保证在用户输入完毕一段时间后再请求接口。

节流：

可以将一些事件降低触发频率。

1、比如懒加载时要监听计算滚动条的位置，但不必每次滑动都触发，可以降低计算的频率；

2、另外还有做商品预览图的放大镜效果时，不必每次鼠标移动都计算位置


