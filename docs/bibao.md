### 闭包的定义
看过一些文章对于闭包的定义，大多晦涩难懂，阮一峰的解释稍微好理解一些：
>>由于在 Javascript 语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成定义在一个函数内部的函数。
>>在使用闭包时，有几点需要明确：
>>1、闭包一定是函数对象
>>2、闭包和词法作用域，作用域链，垃圾回收机制息息相关
>>3、当函数一定是在其定义的作用域外进行的访问时，才产生闭包
>>4、闭包是由该函数和其上层执行上下文共同构成
### 闭包的特性
1、函数的局部变量在函数调用之后仍然可用；
2、栈上的内存空间在函数调用之后仍然存在，不被回收。
### 闭包的用途
1、可以读取函数内部的变量；
2、让这些变量始终保存在内存中。
先来看一个简单的例子：
```
<script type="text/javascript">
    function fn(){
        var a=1;
        add = function (){
            a+=1
        };
        function func(){
            var b=2;
            console.log(a+b)
        }
        return func
    }
    var result = fn();
    result();//3
    add();
    result()//4
</script>
```
在一个函数内，定义了一个新的函数，那么这个新的函数就是一个闭包。

对于C语言或其他语言，函数执行结束并返回之后，它所占用的栈空间将被释放，函数定义的局部变量将不再可用。但是在JS中，不是这样的，函数在执行结束后，它所占的栈空间并不会被全部回收，依然留在内存中。为什么会这样呢？

原因就在于 fn 是 func 的父函数，而 func 被赋给了一个全局变量，这导致 func 始终在内存中，而 func 的存在依赖于 fn，因此 fn 也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。

这段代码中另一个值得注意的地方，就是 add = function() { a += 1 } 这一行。首先，变量 add 前面没有使用 var 关键字，因此 add 是一个全局变量，而不是局部变量。其次，add 的值是一个匿名函数（anonymous function），而这个匿名函数本身也是一个闭包，和 func 处于同一作用域，所以 add 相当于是一个 setter，可以在函数外部对函数内部的局部变量进行操作。
从上面的代码，我们得知：

变量是在内存里的，闭包使用的是内存的引用而不是那块内存的值拷贝。
根据之前的文章，外部函数不能读取内部函数的变量，内部函数能够访问外部函数的变量。如果我们想读取内部函数的值应该怎么办呢？---使用闭包函数

在函数内部定义一个函数，将里面的函数变成闭包函数，然后将函数return出来即可。
来看一个简单案例：

(1)，我们想获取一个数值，在任何地方都可以访问到这个变量，则定义一个全局的变量。
```
<script type="text/javascript">
    var globalNum = 0;
    function add(){
        return globalNum++
    };
    console.log(add())//0
    console.log(add())//1
    console.log(add())//2
</script>
```
定义了全局变量globalNum ，代码任何地方都可以访问到，体验不是很好，把变量放在函数内，定义成局部变量。
```
<script type="text/javascript">
    
    function add(){
        var globalNum = 0;
        return globalNum++
    };
    console.log(add())//0
    console.log(add())//0
    console.log(add())//0
</script>
```
定义成局部变量后，每一次执行都会初始化一下，执行的结果都是0，如果还希望是之前的结果，如何做呢？定义一个闭包函数即可。
```
<script type="text/javascript">
    
    function add(){
        var globalNum = 0;
        var count = function(){
            return globalNum++
        }
        count();
        return globalNum
    };
    
    console.log(add())//1
    console.log(add())//1
    console.log(add())//1
</script>
```
我们在add()函数内部，再定义一个count ()内嵌函数（闭包），内嵌函数count ()可以访问父函数的globalNum变量。现在我们只需要在外部访问count()函数，保证globalNum = 0只执行一次就可以了。
```
<script type="text/javascript">
    
    function add(){
        var globalNum = 0;
        var count = function(){
            return globalNum++
        }
        return count
    };
    var sum = add()
    console.log(sum())//0
    console.log(sum())//1
    console.log(sum())//2
</script>
```
globalNum变量受add()保护，只能通过函数count改变

闭包函数有以下几个特点:

### 1、 外层函数的变量和运算之后，闭包函数才进行运行。
```
<script type="text/javascript">
    function fn(){
        var a=10;
        function fn1(){
            console.log(a)
        }
        a++;
        return fn1()
    }
    fn()//11
</script>
```
当我们将a这个运算写在闭包函数外面的时候,最后的结果确实自增之后的值.这就是闭包函数的一个特点:

### 2、闭包中使用的局部变量,一定是局部变量变化最后的值,

就像上面的变量a在函数内部,同时a也在闭包函数外面,但闭包函数中最后运行的值是所有变量以及其运算符变化之后的值.

### 3、闭包中使用的外层所有局部变量都会存起来用,如果有变量操作在闭包函数中,则不会保存在内存中,只要在调用时,会执行闭包函数的内部代码
```
<script type="text/javascript">
    function fn(){
        var a=10;
        return function fn1(){
            a++;
            console.log(a)
        }
    }
    fn()();//11
    fn()();//11
    var fn2=fn();
    fn2();//11
    fn2();//12
    fn2();//13
</script>
```
从上面的例子我们可以看出,如果将fn()赋值给另一个变量,当多次执行时,值是增加的,也就是多次执行了闭包里面的a++操作.但是我们单纯调用fn()()后,就不会发生这样的事情,我们还可以从以下面的例子来发现这种特点:
## 使用闭包需要注意的要点:
1.当页面跳转的时候,信息一直保存在内存中,不会被释放,对电脑内存的消耗较大,所以不能滥用闭包.否则会影响网页打开速度,影响性能.在IE中变量从DOM节点获取,其属性又去引用一个函数,而内部函数又去引用外部的变量,这种互相引用,就会造成内存泄漏问题.解决办法如下:
我们来解决一下IE的内存泄漏问题.
```
<script type="text/javascript">
    window.onload=function(){
        var oDiv=document.getElementById('div1')
        oDiv.onclick=function(){
            alert(oDiv.id)
        }
    }
    </script>
</head>
<body>
    <div id="div1">11111</div>
</body>
```
比如上面的例子,oDiv从DOM节点获取,然后点击oDiv触发div的id,这样就会造成内存泄漏,解决的办法就是在后面添加上
window.onload=function(){oDiv.onclick=null}这段代码
```
<script type="text/javascript">
    window.onload=function(){
        var oDiv=document.getElementById('div1')
        oDiv.onclick=function(){
            alert(oDiv.id)
        }
        window.onload=function(){
            oDiv.onclick=null;
        }
    }
</script>
```
还有一种解决办法:将值存起来,然后在代码执行完毕后清除掉
```
<script type="text/javascript">
window.onload=function(){
    var oDiv=document.getElementById('div1')
    var id=oDiv.id;
    oDiv.onclick=function(){
        alert(oDiv.id)
    }
    oDiv=null
}
</script>
```
2.闭包会在父函数外部,改变父函数内部变量的值.所以如果把父函数当做对象使用,把闭包当做它的公用方法,把内部变量当做它的私有属性,这个时候不要随便改变父函数内部变量的值.

在实际项目中,我们通常在循环以及对象中使用闭包比较多,有时会造成一些问题.
在循环中使用闭包函数
先来看正常情况
```
<script type="text/javascript">
    var arr=[];
    for(var i=0;i<5;i++){
        arr[i]=i;
    }
    console.log(arr)//[0,1,2,3,4]
</script>
```
正常情况无异议,再来看看循环有函数的情况
```
<script type="text/javascript">
    window.onload=function(){
        var oLi=document.getElementsByTagName('li')
        for(var i=0;i<oLi.length;i++){
            oLi[i].onclick=function(){
                alert(i);//每次点击都会弹出3
            }
        }
    }
</script>
```
比如上例,在HTML有3个li,按照上面的写法,会弹出3次3,也就是length的值.这是因为当我们调用函数的时候,循环内容已经运行完毕,最后的结果是3,如果我们想每次点击获取对应元素的下标,就需要用到闭包函数的解决方法,将值保存起来使用.

上例我们有两种方法可以解决,第一种是利用匿名函数的自身调用.在事件外面套一个匿名函数,然后将i作为参数传进去.这是因为匿名函数的i的值已经保存在内存中,闭包函数调用的时候就使用这些值,不会弹出之前的3,而是依次弹出索引值
```
<script type="text/javascript">
    window.onload=function(){
        var oLi=document.getElementsByTagName('li')
        for(var i=0;i<oLi.length;i++){
            (function(i){
                oLi[i].onclick=function(){
                    alert(i);//依次点击弹出0,1,2
            }
            })(i)
            
        }
    }
</script>
```
第二种方法是将点击事件的函数作为一个匿名函数,在函数内部用return返回一个函数,函数内再执行命令.这是因为点击事件时,将i的值保存在内部,然后再通过调用闭包函数就得到了已经在内存中保存的值.
```
<script type="text/javascript">
    window.onload=function(){
        var oLi=document.getElementsByTagName('li')
        for(var i=0;i<oLi.length;i++){
            
                oLi[i].onclick=(function(i){
                    return function(){
                        alert(i);//依次点击弹出0,1,2
                    }   
            })(i)
            
        }
    }
</script>
```
以上是闭包在循环中的运用.那么闭包在对象中又会产生什么问题呢?
在对象中使用函数,经常会用到this关键字,这就存在this指向问题.
## 在对象中使用闭包
```
<script type="text/javascript">
    var name="lili";
    var obj={
        name:"lilei",
        age:20,
        love:function fn(){
            console.log(this.name)
        }
    }
    obj.love();//lilei
    var obj1={
        name:"lilei",
        age:22,
        love:function(){
            return function(){
                console.log(this.name)
            }
        }
    }
    obj1.love()()//lili
</script
```
从上面两个例子我们可以看出,在对象中,如果是闭包函数,那么它里面的this指向的是window.  如果我们想改变this指向问题,有下面这种方法
我们在函数内部,将this用一个变量that存起来,然后再来访问这个变量.
```
<script type="text/javascript">
    var obj1={
        name:"lilei",
        age:22,
        love:function(){
            var that=this;
            return function(){
                console.log(that.name)
            }
        }
    }
    obj1.love()()//lilei
</script>
```
在对象中的闭包,this都指向window,需要改变this指向问题
以上是使用闭包时的一些问题。那么闭包的优缺点有哪些呢？以及使用场景是什么呢？

优点：
1、避免全局变量的污染
2、调试对变量走势更清晰认识
3、保护函数内变量安全，加强了封装性
闭包之所以会占用资源是当函数a执行结束后, 变量i不会因为函数a的结束而销毁, 因为b的执行需要依赖a中的变量

缺点：
使用闭包会造成内存浪费的问题，这个内存浪费不仅仅因为它常驻内存，更重要的是，对闭包的使用不当会造成无效内存的产生
闭包使用场景
1.使用闭包可以在JavaScript中模拟块级作用域；
2.闭包可以用于在对象中创建私有变量。