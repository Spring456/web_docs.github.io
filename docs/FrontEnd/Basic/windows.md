## 概述
浏览器里面，window对象（注意，w为小写）指当前的浏览器窗口。它也是当前页面的顶层对象，即最高一层的对象，所有其他对象都是它的下属。一个变量如果未声明，那么默认就是顶层对象的属性。

## window对象的属性
### window.name 
window.name表示当前浏览器窗口的名字。窗口不一定需要名字，这个属性主要配合超链接和表单的target属性使用。

该属性只能保存字符串，如果写入的值不是字符串，会自动转成字符串。各个浏览器对这个值的储存容量有所不同，但是一般来说，可以高达几MB。

一旦浏览器窗口关闭后，该属性保存的值就会消失，因为这是窗口已经不存在了。

```
window.name = 'Hello World!';
console.log(window.name)
```
### window.closed
window.closed表示窗口是否关闭。属性返回一个布尔值.

上面代码检查当前窗口是否关闭。这种检查意义不大，因为只要能运行代码，当前窗口肯定没有关闭。这个属性一般用来检查，使用脚本打开的新窗口是否关闭。
```
var popup = window.open();

if ((popup !== null) && !popup.closed) {
  // 窗口仍然打开着
}
```
### window.opener
window.opener属性表示打开当前窗口的父窗口。如果当前窗口没有父窗口（即直接在地址栏输入打开），则返回null。
```
window.open().opener === window // true
```
上面表达式会打开一个新窗口，然后返回true。

<a>元素添加rel="noopener"属性，可以防止新打开的窗口获取父窗口。

### window.self，window.window
window.self和window.window属性都指向窗口本身。这两个属性只读。
```
window.self === window // true
window.window === window // true
```
### window.frames
window.frames属性返回一个类似数组的对象，成员为页面内所有框架窗口，包括frame元素和iframe元素。window.frames[0]表示页面中第一个框架窗口。

如果iframe元素设置了id或name属性，那么就可以用属性值，引用这个iframe窗口。比如<iframe name="myIFrame">可以用frames['myIFrame']或者frames.myIFrame来引用。

frames属性实际上是window对象的别名。
```
frames === window // true
```

因此，frames[0]也可以用window[0]表示。但是，从语义上看，frames更清晰，而且考虑到window还是全局对象，因此推荐表示多窗口时，总是使用frames[0]的写法

### window.length
window.length属性返回当前网页包含的框架总数。如果当前网页不包含frame和iframe元素，那么window.length就返回0。
```
window.frames.length === window.length // true
```
### window.frameElement
window.frameElement属性主要用于当前窗口嵌在另一个网页的情况（嵌入<object>、<iframe>或<embed>元素），返回当前窗口所在的那个元素节点。如果当前窗口是顶层窗口，或者所嵌入的那个网页不是同源的，该属性返回null。
```
// HTML 代码如下
// <iframe src="about.html"></iframe>

// 下面的脚本在 about.html 里面
var frameEl = window.frameElement;
if (frameEl) {
  frameEl.src = 'other.html';
}
```
上面代码中，frameEl变量就是<iframe>元素。
### window.top
window.top属性指向最顶层窗口，主要用于在子窗口里面获取顶层的父窗口。
### window.parent
window.parent属性指向父窗口。如果当前窗口没有父窗口，window.parent指向自身。
### window.status
window.status属性用于读写浏览器状态栏的文本。但是，现在很多浏览器都不允许改写状态栏文本，所以使用这个方法不一定有效。
### window.devicePixelRatio
window.devicePixelRatio属性返回一个数值，表示一个 CSS 像素的大小与一个物理像素的大小之间的比率。也就是说，它表示一个 CSS 像素由多少个物理像素组成。它可以用于判断用户的显示环境，如果这个比率较大，就表示用户正在使用高清屏幕，因此可以显示较大像素的图片。

## 位置大小属性
### （1）window.screenX，window.screenY
window.screenX和window.screenY属性，返回浏览器窗口左上角相对于当前屏幕左上角的水平距离和垂直距离（单位像素）。这两个属性只读。
### （2） window.innerHeight，window.innerWidth
window.innerHeight和window.innerWidth属性，返回网页在当前窗口中可见部分的高度和宽度，即“视口”（viewport）的大小（单位像素）。这两个属性只读。

用户放大网页的时候（比如将网页从100%的大小放大为200%），这两个属性会变小。因为这时网页的像素大小不变（比如宽度还是960像素），只是每个像素占据的屏幕空间变大了，因为可见部分（视口）就变小了。

注意，这两个属性值包括滚动条的高度和宽度。
### （3）window.outerHeight，window.outerWidth
window.outerHeight和window.outerWidth属性返回浏览器窗口的高度和宽度，包括浏览器菜单和边框（单位像素）。这两个属性只读。
### （4）window.scrollX，window.scrollY
window.scrollX属性返回页面的水平滚动距离，window.scrollY属性返回页面的垂直滚动距离，单位都为像素。这两个属性只读。

举例来说，如果用户向下拉动了垂直滚动条75像素，那么window.pageYOffset就是75左右。用户水平向右拉动水平滚动条200像素，window.pageXOffset就是200左右。
```
if (window.scrollY < 75) {
  window.scroll(0, 75);
}
```
### （5）window.pageXOffset，window.pageYOffset
window.pageXOffset属性和window.pageYOffset属性，是window.scrollX和window.scrollY别名。

## 组件属性
window.locationbar：地址栏对象
window.menubar：菜单栏对象
window.scrollbar：窗口的滚动条对象
window.toolbar：工具栏对象
window.statusbar：状态栏对象
window.personalbar：用户安装的个人工具栏对象

## 全局对象属性
>> indow.document：指向document对象
>> window.location：指向Location对象，用于获取当前窗口的 URL 信息。它等同于document.location属性
>> window.navigator：指向Navigator对象，用于获取环境信息
>> window.history：指向History对象，表示浏览器的浏览历史
>> window.localStorage：指向本地储存的 localStorage 数据
>> window.sessionStorage：指向本地储存的 sessionStorage 数据
>> window.console：指向console对象，用于操作控制台
>> window.screen：指向Screen对象，表示屏幕信息

### window.isSecureContext
window.isSecureContext属性返回一个布尔值，表示当前窗口是否处在加密环境。如果是 HTTPS 协议，就是true，否则就是false。

## window对象的方法
### window.alert()，window.prompt()，window.confirm()   
window.alert()、window.prompt()、window.confirm()都是浏览器与用户互动的全局方法。它们会弹出不同的对话框，要求用户做出回应。注意，这三个方法弹出的对话框，都是浏览器统一规定的式样，无法定制。
### （1）window.alert()
window.alert()方法弹出的对话框，只有一个“确定”按钮，往往用来通知用户某些信息。

用户只有点击“确定”按钮，对话框才会消失。对话框弹出期间，浏览器窗口处于冻结状态，如果不点“确定”按钮，用户什么也干不了。

window.alert()方法的参数只能是字符串，没法使用CSS样式，但是可以用\n指定换行。

### （2）window.prompt()
window.prompt()方法弹出的对话框，提示文字的下方，还有一个输入框，要求用户输入信息，并有“确定”和“取消”两个按钮。它往往用来获取用户输入的数据。
```var result = prompt('您的年龄？', 25)```
window.prompt()的返回值有两种情况，可能是字符串（有可能是空字符串），也有可能是null。具体分成三种情况。
>> 用户输入信息，并点击“确定”，则用户输入的信息就是返回值。
>> 用户没有输入信息，直接点击“确定”，则输入框的默认值就是返回值。
>> 用户点击了“取消”（或者按了 ESC 按钮），则返回值是null。

### （3）window.confirm()
window.confirm()方法弹出的对话框，除了提示信息之外，只有“确定”和“取消”两个按钮，往往用来征询用户是否同意。

### window.open()
window.open方法用于新建另一个浏览器窗口，类似于浏览器菜单的新建窗口选项。它会返回新窗口的引用，如果无法新建窗口，则返回null。

open方法一共可以接受三个参数。
```
window.open(url, windowName, [windowFeatures])
```
>> url：字符串，表示新窗口的网址。如果省略，默认网址就是about:blank。
>> windowName：字符串，表示新窗口的名字。如果该名字的窗口已经存在，则占用该窗口，不再新建窗口。如果省略，就默认使用_blank，表示新建一个没有名字的窗口。
>> windowFeatures：字符串，内容为逗号分隔的键值对（详见下文），表示新窗口的参数，比如有没有提示栏、工具条等等。如果省略，则默认打开一个完整 UI 的新窗口。如果新建的是一个已经存在的窗口，则该参数不起作用，浏览器沿用以前窗口的参数。

**window.open()的参数也可以是接口地址，也是相当于打开地址，可以执行下载动能**

### window.close()
window.close方法用于关闭当前窗口，一般只用来关闭window.open方法新建的窗口。
### window.stop()
window.stop()方法完全等同于单击浏览器的停止按钮，会停止加载图像、视频等正在或等待加载的对象。
### window.moveTo()
window.moveTo()方法用于移动浏览器窗口到指定位置。它接受两个参数，分别是窗口左上角距离屏幕左上角的水平距离和垂直距离，单位为像素。
```
window.moveTo(100, 200)
```
上面代码将窗口移动到屏幕(100, 200)的位置。
### window.moveBy()
window.moveBy方法将窗口移动到一个相对位置。它接受两个参数，分布是窗口左上角向右移动的水平距离和向下移动的垂直距离，单位为像素。
```
window.moveBy(25, 50)
```
上面代码将窗口向右移动25像素、向下移动50像素。
### window.resizeTo()
window.resizeTo()方法用于缩放窗口到指定大小。

它接受两个参数，第一个是缩放后的窗口宽度（outerWidth属性，包含滚动条、标题栏等等），第二个是缩放后的窗口高度（outerHeight属性）

### window.resizeBy()
window.resizeBy()方法用于缩放窗口。它与window.resizeTo()的区别是，它按照相对的量缩放，window.resizeTo()需要给出缩放后的绝对大小。
### window.scrollTo()，window.scroll()，window.scrollBy()
window.scrollTo方法用于将文档滚动到指定位置。它接受两个参数，表示滚动后位于窗口左上角的页面坐标。
```
window.scrollTo(x-coord, y-coord)
```
它也可以接受一个配置对象作为参数。
```
window.scrollTo(options)
```
配置对象options有三个属性。
>> top：滚动后页面左上角的垂直坐标，即 y 坐标。
>> left：滚动后页面左上角的水平坐标，即 x 坐标。
>> behavior：字符串，表示滚动的方式，有三个可能值（smooth、instant、auto），默认值为auto。
```
window.scrollTo({
  top: 1000,
  behavior: 'smooth'
});
```
window.scroll()方法是window.scrollTo()方法的别名。

window.scrollBy()方法用于将网页滚动指定距离（单位像素）。它接受两个参数：水平向右滚动的像素，垂直向下滚动的像素。

```
window.scrollBy(0, window.innerHeight)
```
**window.scrollTo(0)表示回到浏览器顶部**

### window.print()
window.print方法会跳出打印对话框，与用户点击菜单里面的“打印”命令效果相同。
### window.focus()，window.blur()
window.focus()方法会激活窗口，使其获得焦点，出现在其他窗口的前面。
```
var popup = window.open('popup.html', 'Popup Window');

if ((popup !== null) && !popup.closed) {
  popup.focus();
}
```

### window.getSelection()
window.getSelection方法返回一个Selection对象，表示用户现在选中的文本。
```
var selObj = window.getSelection();
var selectedText = selObj.toString();
```
使用Selction对象的toString方法可以得到选中的文本。
### window.getComputedStyle()，window.matchMedia()
window.getComputedStyle()方法接受一个元素节点作为参数，返回一个包含该元素的最终样式信息的对象

indow.matchMedia()方法用来检查 CSS 的mediaQuery语句

## 事件
### load 事件和 onload 属性
load事件发生在文档在浏览器窗口加载完毕时。window.onload属性可以指定这个事件的回调函数。
```
window.onload = function() {
  var elements = document.getElementsByClassName('example');
  for (var i = 0; i < elements.length; i++) {
    var elt = elements[i];
    // ...
  }
};
```
在html里，如果是在页面上直接写js，则最好使用window.onload，否则页面的js事件可能会不生效。


