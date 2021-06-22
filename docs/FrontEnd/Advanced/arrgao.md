## 数组的高级方法

### foreach方法

foreach循环，参数有3个值，分别表示数组的每一项，数组索引和数组本身

```
var a = [1, 2, 3, 4, 5]
    a.forEach((v,i,t) => {
        console.log(v)//1,2,3,4,5
        console.log(i)//0,1,2,3,4
        console.log(t)//[1,2,3,4,5]
    })
```
foreach循环有以下特点：

**没有返回值**

```
var a = [1, 2, 3, 4, 5]

var b = a.forEach((v,i,t) => {
  console.log(v)
})

console.log(b)	// undefined
```

**无法中止跳出循环**

1、在循环内部使用break会报错

```
var a = [1, 2, 3, 4, 5]
a.forEach((v,i,t) => {
    if(v==3){
        break //报错，Uncaught SyntaxError: Illegal break statementat Array.forEach (<anonymous>)
    }
})
```

2、使用return false无效

```
var a = [1, 2, 3, 4, 5]
a.forEach((v,i,t) => {
    if(v==3){
        console.log(11)
        return false
    }
    console.log(v)//1,2,4,5
})
```
使用retrun false可以禁止当前此项的输出，但不能中止循环，不改变原数组。

**跳过空位但不会跳过undefined和null**

forEach()方法也会跳过数组的空位。

```
var a = [null, , undefined]
for (let i = 0; i < a.length; i++) {
    console.log('a', a[i]) // null undefined undefined
}
a.forEach(item => {
    console.log('item', item) // null undefined
});
```
for循环不会跳过空位，会认为是undefined，forEach会跳过空位。


**forEach()不会在迭代之前创建数组的副本 如果数组在迭代时被修改了，则其他元素会被跳过**

```
var a = [1,2,3,4,5];
a.forEach(function(el) {
  console.log(el);//1,2,4,5
  if (el === 2) {
    a.shift();
  }
});
```

当到达包含值 "2" 的项时，整个数组的第一个项被移除了，这导致所有剩下的项上移一个位置。因为元素 "4" 现在在数组更前的位置，"3" 会被跳过。 forEach() 不会在迭代之前创建数组的副本。

**改变原数组**

1、单纯修改基本数据类型无效

如果想更改数组元素，可以使用原数组索引的方法更改
```
var a = [1,2,3,4,5];
a.forEach(item=>{
    item=2
})
console.log(a)//1,2,3,4,5
```
下面这种方法就可以改变数组内元素
```
var a = [1,2,3,4,5];
a.forEach((item,index)=>{
    a[index]=2
})
console.log(a)//2,2,2,2,2
```

2、对对象形式可以更改

```
var a = [1,2,3,4,5,{num:1}];
a.forEach(item=>{
    item.num=2
})
console.log(a)//1,2,3,4,5,{num:2}
```


### map()方法

>> 创建一个新的数组，不改变原数组，其结果为该数组中的每个元素都调用一个提供的函数后返回的结果

map()的作用就是'映射'，也就是原数组被'映射'成对应新数组。

map() 方法返回一个新数组

map() 不会对空数组进行检测。

map() 不会改变原始数组。

```
var a = [1,2,3,4,5];
let b=a.map(item=>item*3)
console.log(b)//3,6,9,12,15
```
map可以接收3个参数，分别表示数组元素，索引，数组本身

```
var arr = ["a","b","c","d","e"];
arr.map(function(currentValue,index,arr){
    console.log("当前元素"+currentValue)
　　　　　　　console.log("当前索引"+index)
    console.log("数组对象"+arr)
})
```

**map()方法的使用**

1、基本使用——数值简单求平方

```
// 例子数值项求平方
let data = [1,2,3,4,5];
let newData = data.map(function (item){
    return item * item;

});
console.log(newData);
//箭头函数的写法
let newData2 = data.map(item => item *item);
console.log(newData2);
```

2、用map()调用一个方法的使用

```
// 调用一个方法：把字符串转整数    
let arr1 = ["1", "2", "3"];

function returnInt (element){
    return parseInt(element, 10);
}
let newArr1 = arr1.map(returnInt);
console.log(newArr1);//[1,2,3]
```

3、map()方法用箭头函数容易出现的错误
```
var array1 = [1,4,9,16];
const map1 = array1.map(x => x *2);
console.log(map1);
// 结果为 [2,8,18,32]
```
而我这样写时：有多条语句时

```
var array1 = [1, 4, 9, 16];
 
const map1 = array1.map(x => {
    if (x == 4) {
        return x * 2;
    }
});
 
console.log(map1);
// 打印结果为 [undefined, 8, undefined, undefined]
```
为什么会出现三个undefined呢？而不是我预期的[1,8,9,16]。

这样写只是增加了一个条件，即x的值为4时才乘以2，之所以会出现undefined，是因为map()方法创建了一个新数组，但新数组并不是在遍历完array1后才被赋值的，而是每遍历一次就得到一个值。所以，下面这样修改后就正确了：

```
var array1 = [1, 4, 9, 16];
 
const map1 = array1.map(x => {
    if (x == 4) {
        return x * 2;
    }
    return x;
});
console.log(map1)//[1,8,9,16]
```

这里注意箭头函数有两种格式：

1.只包含一个表达式，这时花括号和return都省略了。

2.包含多条语句，这时花括号和return都不能省略。


4、map()参数为关键字时的情况

一个很常见的面试题：

```
["1", "2", "3"].map(parseInt);
```

结果是什么呢？返回的是[1,2,3]吗？其实返回的是[1,NaN,NaN]，这是为什么呢？

通常使用 parseInt 时，只需要传递一个参数.但实际上，parseInt 可以有两个参数，第二个参数是进制数.

map 方法在调用 callback 函数时，会给它传递三个参数：当前正在遍历的元素，元素索引，原数组本身.

第三个参数 parseInt 会忽视，但第二个参数不会，也就是说：

parseInt 把传过来的索引值当成进制数来使用. 从而返回了 NaN.

如果使用Number关键字

```
['1', '2', '3'].map(Number);  // [1, 2, 3]
```

### reduce()方法

语法：

>> arr.reduce(callback,[initialValue])

