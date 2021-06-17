## 理解JS中的面向对象编程(3)——继承

### 什么是继承？

继承，在js中就是子类继承父类，可以使用父类的属性和方法

### js继承有什么作用

1、可以根据父类的构造方法创造新的实例

2、修改父类的属性或方法可以同步到其所有的子类

### JS继承的方式及优缺点

**一、原型链继承**

```
function Person(name,age){
    this.age=18
}

Person.prototype.getName = function(){
    console.log(this.name);
    console.log(this.age)
}

function Child(){
    this.name = 'Hello world'
}

var child2 = new Person()//父类的实例child2

child2.sex='男'//为实例child2添加sex属性

child2.prototype.age='22'

Child.prototype = new Person();//子类的原型等于父类的实例

var child1 = new Child();//创建子类的实例对象

child1.getName(); // Hello world,18

console.log(child1.sex)//undefined,实例对象child1没有sex这个属性，因此是undefined
```

原型链继承关键点：新实例的原型等于父类的实例

优点：

1、实例可继承的属性有：实例的构造函数的属性(上面的name属性)，父类构造函数属性(上面age属性)，父类原型的属性。

2、新实例不会继承父类实例的属性。(上面子类实例child1不能继承父类实例child2的sex属性)

缺点：

1、新实例无法向父类构造函数传参。

2、继承单一

3、所有新实例都会共享父类实例的属性。（原型上的属性是共享的，一个实例修改了原型属性，另一个实例的原型属性 也会被修改！）

对于第3点，我们用案例来展现

```
function Person () {}
Person.prototype= {
    name:['小明']
}

var a = new Person()
var b = new Person()
a.name.push('小红')
console.log(b.name)//["小明", "小红"]
```
从上面的代码可以得出，实例a修改了原型属性name，实例b的属性也相应改变了。如果使用a.name='小红'这种赋值操作,只是给对象实例添加了一个属性，并不是给原型添加属性，实例b就不会改变其属性。

**二、构造函数继承**
```
function Person(){
    this.name = 'xiaoming';
    this.colors = ['red', 'blue', 'green'];
}

Person.prototype.getName = function(){
    console.log(this.name);
}

function Child(age){
    Person.call(this);
    this.age = age
}

var child1 = new Child(23);
var child2 = new Child(12);
child1.colors.push('yellow');
console.log(child1.name); // xiaoming
console.log(child1.colors); // ["red", "blue", "green", "yellow"]
console.log(child2.colors); // ["red", "blue", "green"]
```
重点：用.call()和.apply()将父类构造函数引入子类函数（在子类 函数中做了父类函数的自执行（复制））

优点：1、只继承了父类构造函数的属性，没有继承父类原型的属性。
　　　2、解决了原型链继承缺点1、2、3。
　　　3、可以继承多个构造函数属性（call多个）。
　　　4、在子实例中可向父实例传参。

缺点：1、只能继承父类构造函数的属性。
　　　2、无法实现构造函数的复用。（每次用每次都要重新调用）
　　　3、每个新实例都有父类构造函数的副本，臃肿。

**三、组合继承（组合原型链继承和借用构造函数继承）（常用）**

```
function Parent(name){
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function(){
    console.log(this.name);
}

function Child(name,age){
    Parent.call(this,name);// 第二次调用 Parent()
    this.age = age;
}

Child.prototype = new Parent(); // 第一次调用 Parent()

var child1 = new Child('xiaopao',18);
var child2 = new Child('lulu',19);
```
重点：结合了两种模式的优点，传参和复用

优点：1、可以继承父类原型上的属性，可以传参，可复用。
　　　2、每个新实例引入的构造函数属性是私有的。

缺点：调用了两次父类构造函数（耗内存），子类的构造函数会代替原型上的那个父类构造函数。

**四、原型式继承**

```
function CreateObj(o){
    function F(){}
    F.prototype = o;
    console.log(o.__proto__ === Object.prototype);
    console.log(F.prototype.constructor === Object); // true
    return new F();
}

var person = {
    name: 'xiaopao',
    friend: ['daisy','kelly']
}

var person1 = CreateObj(person);

// var person2 = CreateObj(person);

person1.name = 'person1';
// console.log(person2.name); // xiaopao
person1.friend.push('taylor');
// console.log(person2.friend); // ["daisy", "kelly", "taylor"]
// console.log(person); // {name: "xiaopao", friend: Array(3)}
person1.friend = ['lulu'];
// console.log(person1.friend); // ["lulu"]
// console.log(person.friend); //  ["daisy", "kelly", "taylor"]
// 注意： 这里修改了person1.name的值，person2.name的值并未改变，并不是因为person1和person2有独立的name值，而是person1.name='person1'是给person1添加了name值，并非修改了原型上的name值
// 因为我们找对象上的属性时，总是先找实例上对象，没有找到的话再去原型对象上的属性。实例对象和原型对象上如果有同名属性，总是先取实例对象上的值
```

重点：用一个函数包装一个对象，然后返回这个函数的调用，这个函数就变成了个可以随意增添属性的实例或对象。object.create()就是这个原理。

优点：类似于复制一个对象，用函数来包装。

缺点：1、所有实例都会继承原型上的属性。
　　　2、无法实现复用。（新实例属性都是后面添加的）

**五、寄生式继承**
```
var ob = {
    name: 'xiaopao',
    friends: ['lulu','huahua']
}

function CreateObj(o){
    function F(){};  // 创建一个构造函数F
    F.prototype = o;
    return new F();
}

// 上面CreateObj函数 在ECMAScript5 有了一新的规范写法，Object.create(ob) 效果是一样的 , 看下面代码
var ob1 = CreateObj(ob);
var ob2 = Object.create(ob);
console.log(ob1.name); // xiaopao
console.log(ob2.name); // xiaopao

function CreateChild(o){
    var newob = CreateObj(o); // 创建对象 或者用 var newob = Object.create(ob)
    newob.sayName = function(){ // 增强对象
        console.log(this.name);
    }
    return newob; // 指定对象
}

var p1 = CreateChild(ob);
p1.sayName(); // xiaopao 
```

重点：就是给原型式继承外面套了个壳子。

优点：没有创建自定义类型，因为只是套了个壳子返回对象（这个），这个函数顺理成章就成了创建的新对象。

缺点：没用到原型，无法复用。

**六、寄生组合式继承（常用）**
```
function Parent(name){
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.sayName = function(){
    console.log(this.name);
}

function Child(name,age){
    Parent.call(this,name); 
    this.age = age;
}

function CreateObj(o){
    function F(){};
    F.prototype = o;
    return new F();
}

// Child.prototype = new Parent(); // 这里换成下面
function prototype(child,parent){
    var prototype = CreateObj(parent.prototype);
    prototype.constructor = child;
    child.prototype = prototype;
}
prototype(Child,Parent);

var child1 = new Child('xiaopao', 18);
console.log(child1); 
```
重点：修复了组合继承的问题

**七、class继承**
```
class Parent5 {
  constructor() {
    this.name = ['super5']
  }
  reName() {
    this.name.push('new 5')
  }
}
class Child5 extends Parent5 {
  constructor() {
    super()
  }
}
var child51 = new Child5()
var child52 = new Child5()
```