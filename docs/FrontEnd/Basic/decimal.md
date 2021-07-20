## JS中小数运算和大整数精度丢失

### 奇怪的问题--浮点数运算或大数的精度问题

在JS中，对小数或较大的数进行运行操作会出现不可预计的问题，比如：

```
0.1+0.2=0.30000000000000004

0.1+0.7=0.7999999999999999

1.2-0.1=1.0999999999999999

0.5-0.4=0.09999999999999998

19.19 * 100=1919.0000000000002

0.3/0.2=1.4999999999999998

console.log(10000000000000011) //10000000000000012
console.log(0.1000000000000001) 
// 0.1000000000000001 (中间14个0，会打印出本身)
console.log(0.10000000000000001) 
// 0.1 (中间15个0，js会认为两个值足够近似，所以输出0.1)
```

刚接触到这个问题的时候，着实令人头大，这是为什么？为什么会发生这样的事情！以后在运算的时候应该怎么办？

原因：

1、在计算机中，数字无论是整数还是浮点数都是以多位二进制的方式进行存储的。在呈现结果时才转换成十进制

2、在JS中数字采用的IEEE 754的双精度标准进行存储，存储一个数值所使用的二进制位数比较多，精度更加精确。

还是上面的例子：0.1+0.2，先分别将0.1和0.2转换为二进制，然后再进行相加，相加得到的结果转为十进制。

```
// 0.1 转化为二进制
0.0 0011 0011 0011 0011...(0011循环）

// 0.2 转化为二进制
0.0011 0011 0011 0011 0011...(0011循环）
```

由于0.1这样的数值用二进制是无法整除的，最后算下来会是 0.000110011…由于存储空间有限，最后计算机会舍弃后面的数值，所以我们最后就只能得到一个近似值。在JS中如果出现这种无法整除的小数就会取一个近似值，在js中如果这个近似值足够近似，那么js就会认为他就是那个值。

还是上面的例子中，由于0.1转换成二进制时是无限循环的，所以在计算机中0.1只能存储成一个近似值。

在js中，除了那些能表示成 x/2^n 的数可以被精确表示以外，其余小数都是以近似值得方式存在的。

在0.1 + 0.2这个式子中，0.1和0.2都是近似表示的，在他们相加的时候，两个近似值进行了计算，导致最后得到的值是0.30000000000000004，此时对于JS来说，其不够近似于0.3，于是就出现了0.1 + 0.2 != 0.3 这个现象。当然，也并非所有的近似值相加都得不到正确的结果。

对于IEEE 754和二进制运行可以参考：https://juejin.cn/post/6981094321759977479

### 如何解决

**1、浮点数变成整数再计算**

将浮点数变成整数再计算，整数是可以精确表示的

```
//0.1+0.2

console.log((0.1*10+0.2*10)/10)//0.3
```
但这种方法（乘以10的N次幂，再除以10的n次幂）并不是万能的，就比如上面19.19*10并不等与1919一样。所以这种方法并不保险。

**2、使用number对象的toFixed方法**

toFixed方法可以指定运算结果的小数点后的指定位数的数字，使保留一位小数就是toFixed(1)

```
//let x=(0.1+0.2).toFixed(1)
//因为使用toFixed方法将number类型转换成了字符串类型
//，所以使用parseFloat将字符串转回number类型
let x=parseFloat((0.1+0.2).toFixed(1));
console.log(x===0.3);
```

使用toFixed()方法可以把Number四舍五入为指定小数位数的数字。但这种方法也并不是万无一失的。比如下面的例子：

```
1.35.toFixed(1) // 1.4 正确
1.335.toFixed(2) // 1.33  错误
1.3335.toFixed(3) // 1.333 错误
1.33335.toFixed(4) // 1.3334 正确
1.333335.toFixed(5)  // 1.33333 错误
1.3333335.toFixed(6) // 1.333333 错误
```
可以看到使用toFixed()方法，也并不是最好的。

**3、将浮点数toString后indexOf('.')**

我们可以将浮点数toString后indexOf('.')，记录一下小数位的长度，然后将小数点抹掉

```
 /*** method **
 *  add / subtract / multiply /divide
 * floatObj.add(0.1, 0.2) >> 0.3
 * floatObj.multiply(19.9, 100) >> 1990
 *
 */
var floatObj = function() {

    /*
     * 判断obj是否为一个整数
     */
    function isInteger(obj) {
        return Math.floor(obj) === obj
    }

    /*
     * 将一个浮点数转成整数，返回整数和倍数。如 3.14 >> 314，倍数是 100
     * @param floatNum {number} 小数
     * @return {object}
     *   {times:100, num: 314}
     */
    function toInteger(floatNum) {
        var ret = {times: 1, num: 0}
        if (isInteger(floatNum)) {
            ret.num = floatNum
            return ret
        }
        var strfi  = floatNum + ''
        var dotPos = strfi.indexOf('.')
        var len    = strfi.substr(dotPos+1).length
        var times  = Math.pow(10, len)
        var intNum = Number(floatNum.toString().replace('.',''))
        ret.times  = times
        ret.num    = intNum
        return ret
    }

    /*
     * 核心方法，实现加减乘除运算，确保不丢失精度
     * 思路：把小数放大为整数（乘），进行算术运算，再缩小为小数（除）
     *
     * @param a {number} 运算数1
     * @param b {number} 运算数2
     * @param digits {number} 精度，保留的小数点数，比如 2, 即保留为两位小数
     * @param op {string} 运算类型，有加减乘除（add/subtract/multiply/divide）
     *
     */
    function operation(a, b, digits, op) {
        var o1 = toInteger(a)
        var o2 = toInteger(b)
        var n1 = o1.num
        var n2 = o2.num
        var t1 = o1.times
        var t2 = o2.times
        var max = t1 > t2 ? t1 : t2
        var result = null
        switch (op) {
            case 'add':
                if (t1 === t2) { // 两个小数位数相同
                    result = n1 + n2
                } else if (t1 > t2) { // o1 小数位 大于 o2
                    result = n1 + n2 * (t1 / t2)
                } else { // o1 小数位 小于 o2
                    result = n1 * (t2 / t1) + n2
                }
                return result / max
            case 'subtract':
                if (t1 === t2) {
                    result = n1 - n2
                } else if (t1 > t2) {
                    result = n1 - n2 * (t1 / t2)
                } else {
                    result = n1 * (t2 / t1) - n2
                }
                return result / max
            case 'multiply':
                result = (n1 * n2) / (t1 * t2)
                return result
            case 'divide':
                result = (n1 / n2) * (t2 / t1)
                return result
        }
    }

    // 加减乘除的四个接口
    function add(a, b, digits) {
        return operation(a, b, digits, 'add')
    }
    function subtract(a, b, digits) {
        return operation(a, b, digits, 'subtract')
    }
    function multiply(a, b, digits) {
        return operation(a, b, digits, 'multiply')
    }
    function divide(a, b, digits) {
        return operation(a, b, digits, 'divide')
    }

    // exports
    return {
        add: add,
        subtract: subtract,
        multiply: multiply,
        divide: divide
    }
}();
```
此方法来自：https://juejin.cn/post/6844903572979597319

**4、使用第三方库**

目前解决这一问题的第三方库有：

a、bignumber

地址：https://github.com/MikeMcl/bignumber.js


