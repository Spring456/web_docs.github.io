## 数组方法

### join()方法
语法：把数组转换成字符串，然后给他规定个连接字符，默认的是逗号( ，)

结果：不改变原数组；重新生成新的数组
```
var arr = [1,2,3];
console.log(arr.join()); 　　　　// 1,2,3
console.log(arr.join("-")); 　　// 1-2-3
console.log(arr); 　　　　　　　　// [1, 2, 3]（原数组不变）
```
### push()方法
语法：向数组末尾添加一个值。

结果：改变原数组
```
var arr = ["Lily","lucy","Tom"];
var count = arr.push("Jack","Sean");
console.log(count); 　// 5
console.log(arr);　// ["Lily", "lucy", "Tom", "Jack", "Sean"]
```
### pop()方法
语法：移除数组最后一项

结果：返回移除的那个值，改变原数组，减少原数组的length
```
var arr = ["Lily","lucy","Tom"];
var item = arr.pop();
console.log(item);  // Tom
console.log(arr);  // ["Lily", "lucy"]
```
### shift()方法
语法：删除原数组第一项

结果：返回删除元素的值；如果数组为空则返回undefined 。
```
var arr = ["Lily","lucy","Tom"];
var item = arr.shift();
console.log(item); 　// Lily
console.log(arr);  // ["lucy", "Tom"]
```
### unshift()方法
语法：将参数添加到原数组开头

结果：返回数组长度，改变原数组
```
var arr = ["Lily","lucy","Tom"];
var count = arr.unshift("Jack","Sean");
console.log(count); // 5
console.log(arr); //["Jack", "Sean", "Lily", "lucy", "Tom"]
```
### sort()方法
语法：对数组的项进行排序

结果：改变原数组
```
var arr1 = ["a", "d", "c", "b"];
console.log(arr1.sort()); 　　　　　　　　　　// ["a", "b", "c", "d"]
```
sort()方法比较的是字符串，没有按照数值的大小对数字进行排序，要实现这一点，就必须使用一个排序函数
```
function sortNumber(a,b)
{
　　return a - b
}
```
```
var arr = [13, 24, 51, 3]; 
console.log(arr.sort()); 　　　　　　　　　　// [13, 24, 3, 51] 
console.log(arr.sort(sortNumber)); 　　　　// [3, 13, 24, 51](数组被改变)
```
sort()函数中，a-b表示升序排列；b-a表示降序排序

### reverse()方法
语法：反转数组项的顺序

结果：改变原数组
```
var arr = [13, 24, 51, 3];
console.log(arr.reverse()); 　　　　　　　　//[3, 51, 24, 13]
console.log(arr); 　　　　　　　　　　　　　　//[3, 51, 24, 13](原数组改变)
```
### concat()方法
语法:合并数组。将参数添加到原数组中。这个方法会先创建当前数组一个副本，然后将接收到的参数添加到这个副本的末尾，最后返回新构建的数组。在没有给 concat()方法传递参数的情况下，它只是复制当前数组并返回副本。

结果：产生新的数组，不改变原数组

```
var arr = [1,3,5,7];
var arrCopy = arr.concat(9,[11,13]);
console.log(arrCopy); 　　　　　　　　　　　　//[1, 3, 5, 7, 9, 11, 13]
console.log(arr); 　　　　　　　　　　　　　　// [1, 3, 5, 7](原数组未被修改)
```
### slice()方法
语法：返回从原数组中指定开始下标到结束下标之间的项组成的新数组。
1、slice()方法可以接受一或两个参数，即要返回项的起始和结束位置

2、在只有一个参数的情况下， slice()方法返回从该参数指定位置开始到当前数组末尾的所有项。

3、如果有两个参数，该方法返回起始和结束位置之间的项——但不包括结束位置的项。

4、从下标0开始

结果：不改变原数组，会重新生成新的数组
```
var arr = [1,3,5,7,9,11];
var arrCopy = arr.slice(1);
var arrCopy2 = arr.slice(1,4);
var arrCopy3 = arr.slice(1,-2);
var arrCopy4 = arr.slice(-4,-1);
console.log(arr); 　　　　　　　　　　　　　　//[1, 3, 5, 7, 9, 11](原数组没变)
console.log(arrCopy); 　　　　　　　　　　　 //[3, 5, 7, 9, 11]
console.log(arrCopy2); 　　　　　　　　　　　//[3, 5, 7]
console.log(arrCopy3); 　　　　　　　　　　　//[3, 5, 7]
console.log(arrCopy4); 　　　　　　　　　　　//[5, 7, 9]
```
只设置了一个参数，也就是起始下标为1，所以返回的数组为下标1（包括下标1）开始到数组最后。

设置了两个参数，返回起始下标（包括1）开始到终止下标（不包括4）的子数组。

设置了两个参数，终止下标为负数，当出现负数时，将负数加上数组长度的值（6）来替换该位置的数，因此就是从1开始到4（不包括）的子数组。 

两个参数都是负数，所以都加上数组长度6转换成正数，因此相当于slice(2,5)。

### splice()方法
语法：

1、删除：指定 2 个参数：要删除的第一项的位置和要删除的项数。从数组的索引0开始

2、插入：可以向指定位置插入任意数量的项，只需提供 3 个参数：起始位置、 0（要删除的项数）和要插入的项。

3、替换：可以向指定位置插入任意数量的项，且同时删除任意数量的项，只需指定 3 个参数：起始位置、要删除的项数和要插入的任意数量的项。插入的项数不必与删除的项数相等。
```
var arr = [1,3,5,7,9,11];
var arrRemoved = arr.splice(0,2);
console.log(arr); 　　　　　　　　　　　　　　　//[5, 7, 9, 11]
console.log(arrRemoved); 　　　　　　　　　　　//[1, 3]
var arrRemoved2 = arr.splice(2,0,4,6);
console.log(arr); 　　　　　　　　　　　　　　　// [5, 7, 4, 6, 9, 11]
console.log(arrRemoved2); 　　　　　　　　　　// []
var arrRemoved3 = arr.splice(1,1,2,4);
console.log(arr); 　　　　　　　　　　　　　　　// [5, 2, 4, 4, 6, 9, 11]
console.log(arrRemoved3); 　　　　　　　　　　//[7]
```
### indexOf()方法和lastIndexOf()方法
indexOf()语法：

1、可以接收一个参数或两个参数。一个参数表示要查找的项；两个参数的第二个参数表示查找起点位置的索引。

2、是全等匹配

结果：查找第一次出现的值。从数组的开始（索引为0）开始向后查找，查找到第一个出现的值后就停止。

lastIndexOf()语法
和indexOf()语法类似，只不过是从数组末尾开始向前查找

```
var arr = [1,3,5,7,7,5,3,1];
console.log(arr.indexOf(5)); 　　　　　　//2
console.log(arr.lastIndexOf(5)); 　　　 //5
console.log(arr.indexOf(5,2)); 　　　　 //2
console.log(arr.lastIndexOf(5,4)); 　　//2
console.log(arr.indexOf("5")); 　　　　 //-1
```
### forEach()方法
语法：
1、对数组进行遍历循环，对数组中的每一项运行给定函数。这个方法没有返回值。参数都是function类型，默认有传参，参数分别为：遍历的数组内容；第对应的数组索引，数组本身。

2、在foreach里不能通过break结束循环。使用foreach循环就会一直循环完成

### map()方法
语法：指'映射'，对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。

```
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.map(function(item){
return item*item;
});
console.log(arr2); 　　　　　　　　//[1, 4, 9, 16, 25]
```   
### filter()方法
语法：'过滤'功能，数组中的每一项运行给定函数，返回满足过滤条件组成的数组。
```
var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
var arr2 = arr.filter(function(x, index) {
return index % 3 === 0 || x >= 8;
}); 
console.log(arr2); 　　　　　　　　//[1, 4, 7, 8, 9, 10]
```
### every()方法
语法：判断数组中每一项都是否满足条件，只有所有项都满足条件，才会返回true。
```
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.every(function(x) {
return x < 10;
}); 
console.log(arr2); 　　　　　　　　//true
var arr3 = arr.every(function(x) {
return x < 3;
}); 
console.log(arr3); 　　　　　　　　// false
```
### some()方法
语法：判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回true。
```
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.some(function(x) {
return x < 3;
}); 
console.log(arr2); 　　　　　　　　//true
var arr3 = arr.some(function(x) {
return x < 1;
}); 
console.log(arr3); 　　　　　　　　// false
```