## 详解JS变量提升和函数提升

在开始之前，我们先看一个例子：

```
console.log(a)//undefined
var a='1'
```

为什么打印出来的是undefined呢？而不是报错呢？因为undefined表明的是声明未定义(赋值)啊？这是为什么呢？再来一个例子：

```
console.log(a)//f a(){}
var a=2
function a(){}
```
在上面的例子中，声明了变量a并且赋值为2，同时声明了函数a，在最上面打印出来的a却是函数a(){},这又是为什么呢？

上面两个例子，原因都体现在js的变量提升/函数声明提升。这和js引擎解析代码有关。

## JS'预解析'

JS引擎在执行代码之前会进行一个'预解析'.具体步骤如下：

1、页面创建全局对象(window对象)

2、加载脚本文件，进行语法分析

3、预编译：

    a、查找变量，查找var关键字，作为window的属性，赋值为undefined

    b、查找函数声明，作为window的属性，赋值为函数体，

    C、如果变量名称和函数名称相同，会被函数声明覆盖掉。函数声明提升优于变量的声明提升。

4、执行代码阶段

    a、变量赋值

    b、查找到函数声明后，值赋值给函数体

    c、如果函数有调用，则执行函数里的代码，执行完毕后，销毁函数声明

    d、重复预编译步骤


### 变量提升

我们了解了JS是如何编译执行代码之后，我们来解析上面的问题.

当我们创建变量时：

```
var a=1
var b=2
```

JS在定义变量时，并不是声明了一个变量就立马复制给变量，而是先将所有的声明完成之后再到定义变量的地方进行赋值。变量的声明的过程就是变量的提升。

上面的代码在执行过程中，其实是这样的：

```
var a;
var b;
a=1;
b=2
```

再来看一个例子

```
var a=3
function foo() {
  var a = 1;
  console.log(a);
  console.log(b);
  var b = 2;
}
foo();
```

解析：

```
function foo() {
  var a;
  var b;
  a = 1;
  console.log(a);//1
  console.log(b);//undefined
  b = 2;
}
foo();
```
变量提升只是在其作用域内，来看下一个例子

```
var a=1
console.log(a)
function b(){
    console.log(a)
    if(!a){
        var a=2
        console.log(a)
    }
    console.log(a)
}
console.log(a)
b()
```

解析过程为及最后的结果为：

```
var a;
a=1
console.log(a)//1
function b(){
    var a;
    console.log(a)//undefined
    if(!a){//a为undefined，所以为真
        a=2
        console.log(a)//2
    }
    console.log(a)//2
}
console.log(a)//1
b()
```

上面的例子说明了：变量声明在其作用域内才进行变量声明提升

### 函数声明提升

变量的提升分两步：第一步是变量声明的提升；第二步是变量的赋值；

对于函数声明提升，分为这几步：

1、将直接将整个函数整体提升到作用域的最开始位置。

2、变量名称和函数名称相同时，函数名称覆盖变量名称，函数体覆盖变量赋值

3、再解析到函数是否被调用，只有函数被调用的时候才执行函数体内部的代码。

4、函数被调用之后，这个函数声明就会被销毁，有声明，但并未赋值。预解析从头开始。

首先来看个简单的例子：

```
function foo() {
  console.log(a);
  var a = 1;
  console.log(a);
  function a() {}
  console.log(a);
}
foo();
```
上面的代码在js眼中是这样解析的：

```
function foo() {
  var a;
  function a() {}
  console.log(a); // a(){}
  a = 1;
  console.log(a); // 1
  console.log(a); // 1
}
foo();
```

如果碰到了函数调用，则执行函数体里的代码。当函数调用之后，函数被销毁

```
console.log(a) 
a(); 
var a = 1;
function a() {    
    console.log(2)
};
console.log(a) 
a = 3;
a(); 
```

js解析如下

```
var a;
function a() {    
    console.log(2)
};
console.log(a) // 最后的声明为函数声明， 因此a此时为函数体
a(); // 执行 a 函数，输出2.此时变量a的值销毁，a声明但未定义
var a;
a = 1; // 1 赋给a
console.log(a) // 1
a = 3; // 3赋给a，不是一个函数，故下方执行throw error
console.log(a)//3
a(); // throw error
```











