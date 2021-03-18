在JS中，this的指向一直是个问题，理解this指向，始终坚持一个原理：**this 永远指向最后调用它的那个对象**。
## 全局环境中调用
在全局环境中，在任何函数外面，this指代全局对象，也就是window
```
console.log(this===window)//true
```
## 普通函数调用
在普通函数中，this同样指代的是window
```
<script>
    function fn(){
        var name = 'abc';
        console.log(this);
        console.log(this.name)
    }
    fn()//输出 window,abc
</script>
```
```
<script>
   var a = 10;
   function fn(){
      var a = 20;
      console.log(this.a)
   }
   fn()//10
</script>
```
看这两段代码，我们先默念一遍：**this 永远指向最后调用它的那个对象**。   
同样的，这段代码在全局对象window下声明了一个name属性，在普通函数中调用也还是调用的window下的方法，因此这个this照样指向的是window
## 对象中的this
```
<script>
    var obj={
        name:'abc',
        fn:function(){
            return this.name
        }
    }
    console.log(obj.fn())//abc
</script>
```
这段代码中，对象中包含了一个属性和一个方法，方法中的this指代调用函数的那个对象。对象obj调用fn的方法，所以这里的this指代的是obj。this.name=obj.name
在何处定义函数并不影响this的指向，我们也可以先定义函数，再调用方法；
```
<script>
    var fn = function(){
        return this.name
    };
    var obj = {name:'abc'};
    obj.f=fn;
    console.log(obj.f())//abc
</script>
```
这段代码先定义了函数fn，然后定义对象obj，将fn赋值到obj.f，这样也是obj这个对象调用f的方法，this依然指向的是window
如果我将函数的方法赋值给一个变量，用这个变量去调用这个方法，此时这个this指向的是谁呢？
```
<script>
   var name = 'ABC'
   var obj = {
      name:'abc',
      fn:function(){
         console.log(this.name)
      }
   }
   var newObj = obj.fn;
   newObj();//ABC
</script>
```
结果是全局对象下的值，这是因为obj.fn方法赋值给newObj变量，此时newObj变量相当于window对象的一个属性，调用newObj方法相当于是window.newObj()，所以这个this指向的是window对象。
this.name = window.name
我们牢记：
>谁调用函数的属性或方法，this就指向这个调用的对象
## 闭包中的this
结论：**闭包中的this指向的是window**
```
<script>
   var name = 'age'
   function fn(){
      var a= 10;
      var name = 'AGE'
      function func(){
         var b=20;
         var name = 'AGES'
         console.log(this.name+':'+(a+b))
      }
      return func();
   }
   fn()//age:30;this指向window
</script>
```
谈到闭包函数，再强调两点：
1、谁调用，this指向谁
2、闭包函数不属于任何对象，它不是一个对象的方法（就不能使用点运算符），如果没有指定属于哪个对象，那么它就属于全局window对象。
上段代码中，我们调用fn()方法后，是直接return出了func()函数，相当于直接调用，没有通过哪个对象，因此属于func()函数中的this相当于window对象。
```
<script>
var obj={
    b:1,
    fn:function a(){
        var b=2;
        console.log(this.b);   //this指向obj
        console.log(b)        //fn下变量b的值
        return function c(){
            console.log(this.b);//弹出window对象的b属性
       }
    }
}
var b=3;
var q=obj.fn();//先弹出1,再弹出2。此式子得到了闭包c，c==q
q();        //弹出3，c的调用，相当于c();
//w(或者说a)可以通过.由对象t调用，闭包c的调用直接发生在window中，相当于window.q()
</script>
```
上面obj对象里的fn方法，需要通过点运算符由obj调用，就是obj.fn()，而obj.fn()得到了闭包c。此时直接调用c就可以了：obj.fn()()，相当于c()。注意c是直接调用的，没有通过哪个对象，所以默认属于window。请注意，：obj.fn()()中的点运算符调用的是fn，与闭包无关。
再看一个例子：
```
var name = "windowsName";
    function fn() {
        var name = 'Cherry';
        innerFunction();
        function innerFunction() {
            console.log(this.name);      // windowsName
        }
    }
    fn()
```
上例中，fn()调用后，触发innerFunction()，这个函数前面也没有谁调用，所以依然是window调用的，所以打印全局的变量。
## 改变this的指向
改变this的指向，主要有以下几种情况：
1. 使用ES6箭头函数
2. 在函数内部使用_this=this
3. 使用apply(),call(),bind()
4. new实例化一个对象
```
var name = "Windows";
    var a = {
        name : "Hello Word",
        fn1: function () {
            console.log(this.name)     
        },
        fn2: function () {
            setTimeout( function () {
                this.fn1()
            },100);
        }
    };
    a.fn2()     // this.fn1 is not a function
```
注:
setTimeout/setInterval/匿名函数执行的时候，this默认指向window对象，除非手动改变this的指向。
在《javascript高级程序设计》当中，写到：“超时调用的代码(setTimeout)都是在全局作用域中执行的，因此函数中的this的值，在非严格模式下是指向window对象，在严格模式下是指向undefined”。本文都是在非严格模式下的情况。
因此上面的例子中，在全局window下，没有fn1()这个函数，因此报错。如何改写：
### 使用箭头函数
es6里面this指向固定化，始终指向外部对象，因为箭头函数没有this,因此它自身不能进行new实例化,同时也不能使用call, apply, bind等方法来改变this的指向
```
var a = {
  name : "Hello word",
  fn1: function () {
    console.log(this.name)
  },
  fn2: function () {
    setTimeout(()=> {
      this.fn1()
    },100);
  }
};
a.fn2() // Hello word
```
箭头函数的 this 始终指向函数定义时的 this，而非执行时。箭头函数需要记着这句话：“箭头函数中没有 this 绑定，必须通过查找作用域链来决定其值，如果箭头函数被非箭头函数包含，则 this 绑定的是最近一层非箭头函数的 this，否则，this 为 undefined”。
### 使用_this=this
```
var a = {
  name : "Hello word",
  fn1: function () {
    console.log(this.name)
  },
  fn2: function () {
    var _this= this;
    setTimeout(function() {
      _this.fn1()
    },100);
  }
};
a.fn2()            // Hello word
```
这里的_this是调用函数fn2()的对象a，因此指向的也是对象a
### 使用call() 和 apply()
call() 和 apply() 是函数对象的方法，它的作用是改变函数的调用对象，它的第一个参数表示改变后的调用这个函数的对象，也就是要指向的对象。因此，this 指代的就是这两个方法的第一个参数;如果第一个参数为空，那就指向window。
```
var x = 0;　　
function f() {　　　　
    console.log(this.x);　　
}　　
var o = {};　　
o.x = 1;
o.m = f;　　
o.m.apply(); // 0
```
call() 和 apply() 的参数为空时，默认调用全局对象。因此，这时的运行结果为 0，证明 this 指的是全局对象。如果把最后一行代码修改为：
```
o.m.apply(o); // 1
```
运行结果就变成了 1，证明了这时 this 指代的是对象 o。
上面的例子可以改写成：
```
var a = {
  name : "Hello word",
  fn1: function () {
    console.log(this.name)
  },
  fn2: function () {
    setTimeout(  function () {
      this.fn1()
    }.call(a),100);
  }
};
a.fn2()            // Hello word
```
### 使用apply()
```
var a = {
  name : "Hello word",
  fn1: function () {
    console.log(this.name)
  },
  fn2: function () {
    setTimeout(  function () {
      this.fn1()
    }.apply(a),100);
  }
};
a.fn2()            // Hello word
```
使用bind()
ECMAScript 5 引入了 Function.prototype.bind。调用 f.bind(someObject) 会创建一个与 f 具有相同函数体和作用域的函数，但是在这个新函数中，this 将永久地被绑定到了 bind 的第一个参数，无论这个函数是如何被调用的。如下代码所示：
```
function f() {
    return this.a;
}
var g = f.bind({
    a: "stone"
});
console.log(g()); // stone
var o = {
    a: 28,
    f: f,
    g: g
};
console.log(o.f(), o.g()); // 28, stone
```
使用bind改写上面的例子：
```
var a = {
  name : "Hello word",
  fn1: function () {
    console.log(this.name)
  },
  fn2: function () {
    setTimeout(  function () {
      this.fn1()
    }.bind(a),100);
  }
};
a.fn2() // Hello word
```
三种不同方法，写法相同，那apply(),call(),bind()三者有什么异同呢？
apply()和call()
其实 apply 和 call 基本类似，他们的区别只是传入的参数不同。
call 的语法为：
```
fun.call(thisArg[, arg1[, arg2[, ...]]])
```
所以 apply 和 call 的区别是 call 方法接受的是若干个参数列表，而 apply 接收的是一个包含多个参数的数组。
### bind 和 apply、call 区别
```
var a ={
  name : "Cherry",
  fn : function (a,b) {
    console.log( a + b)
  }
}
var b = a.fn;
b.bind(a,1,2)
```
我们会发现并没有输出，这是为什么呢?bind()方法创建一个新的函数, 当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。所以我们可以看出，bind 是创建一个新的函数，我们必须要手动去调用：
```
var a ={
  name : "Cherry",
  fn : function (a,b) {
    console.log( a + b)
  }
}
var b = a.fn;
b.bind(a,1,2)()           // 3
```
## this在实际工作中的注意事项
### DOM 事件处理函数中的 this
一般来讲，当函数使用 addEventListener，被用作事件处理函数时，它的 this 指向触发事件的元素。如下代码所示：
```
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title>test</title>
</head>
<body>
    <button id="btn" type="button">click</button>
    <script>
        var btn = document.getElementById("btn");
        btn.addEventListener("click", function(){
            this.style.backgroundColor = "#A5D9F3";
        }, false);
    </script>
</body>
</html>
```
但在 IE 浏览器中，当函数使用 attachEvent ，被用作事件处理函数时，它的 this 却指向 window。如下代码所示：
```
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title>test</title>
</head>
<body>
    <button id="btn" type="button">click</button>
    <script>
        var btn = document.getElementById("btn");
        btn.attachEvent("onclick", function(){
            console.log(this === window);  // true
        });
    </script>
</body>
</html>
```
### 内联事件处理函数中的 this
当代码被内联处理函数调用时，它的 this 指向监听器所在的 DOM 元素。如下代码所示：
```
<button onclick="alert(this.tagName.toLowerCase());">
  Show this
</button>
```
上面的 alert 会显示 button，注意只有外层代码中的 this 是这样设置的。如果 this 被包含在匿名函数中，则又是另外一种情况了。如下代码所示：
```
<button onclick="alert((function(){return this})());">
  Show inner this
</button>
```
在这种情况下，this 被包含在匿名函数中，相当于处于全局上下文中，所以它指向 window 对象。