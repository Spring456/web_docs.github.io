## 理解JS中的面向对象编程——封装(2)

### 面向对象的特征(优势)

一、封装 

a、封装就是把数据(属性)和方法组合到一个对象里面

b、对象就是将属性和方法封装起来

c、方法是将过程封装起来

二、继承

a、子类继承父类，可以继承父类的方法及属性，实现的是子类可以使用父类的方法和属性。

b、在js中简单的继承模式：混入模式；在jq中继承是extend


三、多态

接口的多种不同的实现方式即为多态。根据实现方法的对象，相同方法名具有不同的行为。通过接收的参数变化，来实现多态。 

### 实现面向对象编程——封装

在实现面向对象时，需要对数据进行封装。数据和方法组合在对象中。

1、使用对象的原始模式

```
var people={
    name:'小明',
    age:20,
    say:function(){console.log('hello')}
}
```
这是最简单的封装，将两个属性和一个方法组合在一个对象中。如果需要多生成几个实例，就需要多写几个，比较麻烦，而且实例和这个对象的原型也没有联系。

2、用函数返回对象的方式

```
function people(name,age){
    return{
        name:name,
        age:age,
        say:function(){console.log('hello')}
    }
}
```
```
var p1 = people('小明','20');//{name: "小明", age: 20, say: ƒ()}
var p2 = people('小红','20');//{name: "小红", age: 20, say: ƒ()}
```

通过调用函数的方式，生成实例对象。这个方法等于是调用函数，返回一个对象。这个方法的问题在于p1和p2之间没有联系，不能反应出他们是同一个原型对象的实例。

3、使用构造函数方式创建对象

什么是构造函数？其实就是一个普通函数，但是内部使用了this变量。对构造函数使用new操作符，就能生成实例，并且this变量会绑定在实例对象上。

使用构造函数改写上面的例子

```
function people(name,age){
    this.name=name
    this.age = age
    this.say = function(){console.log('hello')}
}
```
```
var p1 = new people('小明','20')//{name: "小明", age: "20", say: ƒ}
var p2 = new people('小红','20')//{name: "小红", age: "20", say: ƒ}
console.log(p1.constructor==p2.constructor)//true
```

而且我们发现，采用这种方式，p1,p2自动会含有一个constructor属性，这个属性指向people这个构造函数

4、构造函数的问题

构造函数有一个问题就是浪费内存；每次生成的实例，都指向不同的内存。

比如上面的例子中，
```
console.log(p1.say()==p2.say())//false
```
因为实例调用的方法指向的并不是同样的内存。如何实现指向的是同一个内存？采用Prototype模式

5、Prototype模式

Javascript规定，每一个构造函数都有一个prototype属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。

这意味着，我们可以把那些不变的属性和方法，直接定义在prototype对象上。

```
function people(name,age){
    this.name=name
    this.age = age
}
//people可以获取原型对象，然后在其原型对象上添加say方法，这样通过new创建的对象就都有这个对象原型上的say方法。
people.prototype.say=function(){console.log('hello')}
```
然后我们就发现：
```
console.log(p1.say()==p2.say())//true
```

这是因为实例p1和实例p2的say方法指向的都是构造函数people的prototype属性(原型的方法)。

总结：

1、目前在创建对象时，比较流行的方法就是构造函数+原型模式。

2、构造函数+原型模式中，有同样的方法或属性添加到原型上，变化的方法或属性可以用构造函数实现。

3、实际工作中，以使用vue项目来说。如果在全局使用一个方法，但又不想在每个页面中引入，这个时候我们可以将这个方法挂载到vue的原型上，然后在需要调用的时候，使用this.xxx就可以调用了。




