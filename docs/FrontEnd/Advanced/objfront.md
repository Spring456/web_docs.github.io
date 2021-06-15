### 简单理解面向对象编程和面向过程编程（1）

### 什么是面向过程编程和面向对象编程？

**面向过程编程：**

就是分析出解决问题所需要的步骤。用函数将这些步骤一步一步实现，使用的时候再依次调用。

**面向对象编程：**

解决问题中的事务分成一个个对象，然后由这些对象分工合作。

如果单独看上面的问题还是比较难以理解的，我们往下看：

### 面向对象编程需要知道什么？

在使用面向对象编程之前，默认已经掌握了如下知识点：

1、理解对象及访问对象的方法

2、this的理解

3、构造函数和对象实例

4、原型链

5、prototype、constructor属性

6、原型继承等

### 面向过程和面向对象的示例

需求：需要创建若干个div并设置样式

**面向过程编程的方式**

```
var div = document.createElement('div');
documet.appendChild(div);
div.style.width = '200px';
div.style.height = '200px';
div.style.backgroundColor = 'red';
div.style.border = '10px dashed blue';
```

我们看到，面向过程编程的方式，是依次创建，依次设置相关属性,对于只设置几个div，依次创建即可。对于需要多个同样div来说可以使用循环也是可以的。

**面向对象编程的方式**

```
function DivTag(){
    // 注意这里的 this 指的是 通过构造函数创建出来的实例对象
    this.DOM = document.createElement('div');
    appendTo : function(node){
        // this是实例对象, 不是DOM节点, 不能直接使用DOM属性和方法
        node.appendChild(this.DOM);
    };
    css : function(option){
        for(var key in option){
            this.DOM.style[key] = option[key];
        }
    }
}
var divTag = new DivTag();
divTag.appendTo(document.body);
divTag.css({
    width : '200px',
    height : 200px,
    backgroundColor : 'red',
    border : '10px dashed blue'
});
```
咋一看，面向对象编程似乎写的代码比面向过程编程写的代码量多多了。难道面向对象编程是比较麻烦的吗？

其实不然，如果我们需要创建若干个不同宽高，不同颜色等不同样式的div，如果使用面向过程编程时，要么就会写若干组有相同代码的代码组，要么在同一个代码组中使用若干判断等，显得代码比较臃肿。

面对这样的需求，如果我们使用面向对象编程，我们操作的思路是：

1、把div看做是一个对象，这个对象上有若干属性

2、新创建的元素有div这个对象身上的属性和方法。

3、不同的元素，我们只需要修改其属性和方法即可。


再举个例子：

场景：我洗衣服

面向过程编程的步骤依次是：

1、我泡衣服

2、我放洗衣粉

3、我把衣服放进洗衣机

4、洗衣机洗衣服

如果使用面向对象编程：

1、我，有哪些属性或方法：泡、放洗衣粉、放进洗衣机这3个方法

2、洗衣机，有哪些属性或方法：洗衣服方法

3、衣服，有哪些属性或方法：衣服

4、将3个对象结合实现我洗衣服的事件

### 面向过程与面向对象对比

面向过程编程：

优点：性能比面向对象高，适合跟硬件联系很紧密的东西，例如单片机就采用的面向过程编程。代码直观

缺点：不易维护、不易复用、不易扩展

面向对象编程：

优点：易维护、易复用、易扩展，由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统 更加灵活、更加易于维护

缺点：性能比面向过程低

### 面向对象编程适用的场景

其实面向对象编程可以适用在很多场景。但我个人认为，面向对象编程非常适合处理一些有若干个且有共同属性或方法的事务身上。

和面向过程相比，面向对象编程总的来说，就是建立对象思想，这个对象里面集成了属性，方法，并提供一个作用域this，然后通过this拿到对象在某个时刻的属性值或方法。

### 躁动的小球实例

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title></title>
        <style type="text/css">
            * {
                margin: 0;
                padding: 0;
            }
            body{
                background: darkmagenta;
            }
            #bg {
                position: relative;
                width: 1000px;
                height: 500px;
                margin: 40px auto;
                background: #000;
                cursor: pointer;
                border-radius:15px;
                opacity: 0.9;
    box-shadow:0 -10px 10px 0 red,10px 0 10px 0 yellow,0 10px 10px 0 green,-10px 0 10px 0 blue;

    -webkit-box-shadow:0 -10px 10px 0 red,10px 0 10px 0 yellow,0 10px 10px 0 green,-10px 0 10px 0 blue;
}
            .boll {
                position: absolute;
                border-radius: 50%;
                transition: all .1s;
            }
        </style>
    </head>

    <body>
        <div id="bg">
        </div>
    </body>
    <script type="text/javascript">
        //获取对象属性
        function getStyle(obj, attr) {
            if(obj.currentStyle) {
                return obj.currentStyle[attr];
            } else {
                return getComputedStyle(obj, false)[attr];
            }
        }
        function CreatBoll(left, top) {
            this.wh = returnRandom(10, 50);
            this.left = left; //||returnRandom(10,600);
            this.top = top; //||returnRandom(0,400);
            this.background = returnRandom(0, 999999);
            this.speedX = returnRandom(-5, 10);
            this.speedY = returnRandom(-5, 5);
            this.alpha = returnRandom(0, 1)
            this.div = document.createElement("div");
        }
        var bg = document.getElementById("bg");
		// 每个小球都有的属性，只是属性值不同，属性挂载到原型上，方便所有小球都可以使用
        CreatBoll.prototype.paint = function() {
            this.div.style.width = this.wh + "px";
            this.div.style.height = this.wh + "px";
            this.div.style.left = this.left + "px";
            this.div.style.top = this.top + "px";
            this.div.style.background = "#" + this.background;
            this.div.className = "boll";
            bg.appendChild(this.div);
        }
		// 每个小球都有的方法
        CreatBoll.prototype.move = function() {
                var my = this;
                window.setInterval(function() {
                    if(my.div.offsetLeft + my.div.offsetWidth > bg.offsetWidth || my.div.offsetLeft < 0) {
                        my.speedX *= -1;
                    }
                    if(my.div.offsetTop + my.div.offsetHeight > bg.offsetHeight || my.div.offsetTop < 0) {
                        my.speedY *= -1;
                    }
                    my.div.style.left = my.div.offsetLeft + my.speedX + "px";
                    my.div.style.top = my.div.offsetTop + my.speedY + "px";

                }, 60);

            }
            //随机数的产生
        function returnRandom(min, max) {
            return parseInt(Math.random() * (max - min) + min)
        }

        //监听一下鼠标的点击事件， 在点击处让小球以爆炸的形式散开        
        bg.onclick = function(ev) {
            var ev = ev || window.event;
            var mLeft = ev.clientX - this.offsetLeft;
            var mTop = ev.clientY - this.offsetTop;
            for(var i = 0; i < 20; i++) {
                var boll = new CreatBoll(mLeft, mTop);//实例，能使用原型上的属性和方法
                boll.paint();
                boll.move();

            }
        }
    </script>
</html>
```

上面的例子就是使用了面向对象编程的思路。若干个小球都有同样的属性和方法，但每个小球的属性值不尽相同。在这样的场景下，使用面向对象开发是非常方便的。


