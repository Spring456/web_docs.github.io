## 基本数据类型和引用数据类型
基本数据类型：number，string，boolean，null，undefined，symbol

引用数据类型：统称为Object类型，细分的话，有：Object，Array，Date，Function等。

## 堆栈

基本数据类型保存在栈内存，栈内存中分别存储着变量的标识符以及变量的值。
```
let a='A'
```
![alt 基本数据类型](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f3345275880a4df09b811d0045a215da~tplv-k3u1fbpfcp-watermark.image)

引用数据类型保存在栈内存，形式如下：名存在栈内存中，值存在于堆内存中，但是栈内存会提供一个引用的地址指向堆内存中的值。
```
let a = {name:'A'};
```
![alt 引用数据类型](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6519a65dccbf46ec816b567d9bb21c08~tplv-k3u1fbpfcp-watermark.image)

## 不同类型的赋值操作
基本数据类型：
```
let a = 1;当你let b = a 时，栈内存会新开辟一个内存，例如这样：
```
![alt 基本类型](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d6c669502c9b4e81a80162442048d308~tplv-k3u1fbpfcp-watermark.image)

引用数据类型
```
let a = {name: 'A',age: 10};
let b = a;
a.age = 20;

```
此时改变a的值，会改变b的值，此时内存中是这样的：
![alt 引用类型](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6526400515a44cc580b0e5c12f7591fd~tplv-k3u1fbpfcp-watermark.image)

## 深浅拷贝
所有基本数据类型的拷贝都是深拷贝

引用数据类型分深浅拷贝

**浅拷贝**：创建一个新的数据，这个数据有着原始数据属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址，所以如果其中一个数据改变了这个地址，就会影响到另一个数据。

> 创建一个新的数据，这个数据有着原始数据属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址，所以如果其中一个数据改变了这个地址，就会影响到另一个数据。

**深拷贝**：深拷贝会拷贝所有的属性，并拷贝属性指向的动态分配的内存。当对象和它所引用的对象一起拷贝时即发生深拷贝。深拷贝相比于浅拷贝速度较慢并且花销较大。在堆中重新分配内存，拥有不同的地址，且值是一样的，复制后的对象与原来的对象是完全隔离，互不影响。

## 深浅拷贝的实现方式



### Object.assign()
Object.assign() 方法可以把任意多个的源对象自身的可枚举属性拷贝给目标对象，然后返回目标对象。但是 Object.assign() 进行的是浅拷贝，拷贝的是对象的属性的引用，而不是对象本身。
对对象的拷贝，只是第一层进行拷贝，对其他层不进行拷贝，所以是浅拷贝。

```
var obj = { a: {a: "hello", b: 21} };
var initalObj = Object.assign({}, obj);
initalObj.a.a = "changed";
console.log(obj.a.a); // "changed"
```
### concat()方法
该方法可以连接两个或者更多的数组，但是它不会修改已存在的数组，而是返回一个新数组。

```
const originArray = [1,2,3,4,5];
const cloneArray = originArray.concat();
console.log(cloneArray === originArray); // false
cloneArray.push(6); // [1,2,3,4,5,6]
console.log(originArray); [1,2,3,4,5];
```
看上去是深拷贝的。
我们来考虑一个问题，如果这个对象是多层的，会怎样。
```
const originArray = [1,[1,2,3],{a:1}];
const cloneArray = originArray.concat();
console.log(cloneArray === originArray); // false
cloneArray[1].push(4);
cloneArray[2].a = 2; 
console.log(originArray); // [1,[1,2,3,4],{a:2}]
```
originArray 中含有数组 [1,2,3] 和对象 {a:1}，如果我们直接修改数组和对象，不会影响 originArray，但是我们修改数组 [1,2,3] 或对象 {a:1} 时，发现 originArray 也发生了变化。
结论：concat 只是对数组的第一层进行深拷贝。

类似的还有数组的slice(0)方法
### ... 展开运算符
```
const originArray = [1,2,3,4,5,[6,7,8]];
const originObj = {a:1,b:{bb:1}};
const cloneArray = [...originArray];
cloneArray[0] = 0;
cloneArray[5].push(9);
console.log(originArray); // [1,2,3,4,5,[6,7,8,9]]
const cloneObj = {...originObj};
cloneObj.a = 2;
cloneObj.b.bb = 2;
console.log(originObj); // {a:1,b:{bb:2}}
```
结论：... 实现的是对象第一层的深拷贝。后面的只是拷贝的引用值。
## 深拷贝
### 1.JSON.parse(JSON.stringify())

用JSON.stringify将对象转成JSON字符串，再用JSON.parse()把字符串解析成对象，一去一来，新的对象产生了，而且对象会开辟新的栈，实现深拷贝。
```
let a=[1,2,3,4,{age: 1}];
let b=JSON.parse(JSON.stringify(a));
a[0]=2;
a[4].age=2;
console.log(a,b);

// (5) [2, 2, 3, 4, {…}]
// 0: 2
// 1: 2
// 2: 3
// 3: 4
// 4: {age: 2}
// length: 5
// __proto__: Array(0)
 
// (5) [1, 2, 3, 4, {…}]
// 0: 1
// 1: 2
// 2: 3
// 3: 4
// 4: {age: 1}
// length: 5
// __proto__: Array(0)

```
该方法有几个缺陷
1、会忽略undefined、symbol和函数，例：
```
let obj = {
    name: 'A',
    name1: undefined,
    name3: function() {},
    name4:  Symbol('A')
}
let obj2 = JSON.parse(JSON.stringify(obj));
console.log(obj2); // {name: "A"}

```
2、对象循环引用时，会报错。例：
```
  let obj = {
      name1: 'A',
      name2: {
          name3: 'B'
      },
  }
  obj.name1 = obj.name2;
  obj.name2.name3 = obj.name1;
  let obj2 = JSON.parse(JSON.stringify(obj));
  console.log(obj2); // Converting circular structure to JSON

```
3、new Date，转换结果不正确
4、正则会被忽略
### 递归函数

```
function deepClone(obj){
    let objClone = Array.isArray(obj)?[]:{};
    if(obj && typeof obj==="object"){
        for(key in obj){
            if(obj.hasOwnProperty(key)){
                //判断obj子元素是否为对象，如果是，递归复制
                if(obj[key]&&typeof obj[key] ==="object"){
                    objClone[key] = deepClone(obj[key]);
                }else{
                    //如果不是，简单复制
                    objClone[key] = obj[key];
                }
            }
        }
    }
    return objClone;
}    

```
### 使用Object.create()方法
直接使用var newObj = Object.create(oldObj)，可以达到深拷贝的效果。
```
/* ================ 深拷贝 ================ */
function deepClone(initalObj, finalObj) {
    var obj = finalObj || {};
    for (var i in initalObj) {
        var prop = initalObj[i];
        // 避免相互引用对象导致死循环，如initalObj.a = initalObj的情况
        if(prop === obj) {
            continue;
        }
        if (typeof prop === 'object') {
            obj[i] = (prop.constructor === Array) ? [] : Object.create(prop);
        } else {
            obj[i] = prop;
        }
    }
    return obj;
}
```
总结
赋值运算符 = 实现的是浅拷贝，只拷贝对象的引用值；
JavaScript 中数组和对象自带的拷贝方法都是“首层浅拷贝”；
JSON.stringify 实现的是深拷贝，但是对目标对象有要求；
若想真正意义上的深拷贝，请递归。
### lodash.js实现深拷贝

文章部分内容摘自:https://juejin.cn/post/6906369563793817607