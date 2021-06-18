## history.pushState()和 history.replaceState()方法

### 浏览器历史记录事件

**1、后退**

window.history.back()——返回到上一页，相当于用户点击浏览器的返回按钮

**2、前进**

window.history.forward() ——前进到下一页，相当于用户点击浏览器的前进按钮

**3、移动到指定的历史记录点**

window.history.go() ——参数可以是正数也可以是负数，表示前进或后退到第几页

通过go()方法，指定一个相对于当前页面位置的数值，从当前会话的历史记录中加载页面（当前位置页面索引：0，上一页：-1，下一页：1）；

window.history.go(-2)--后退2页，相当于调用两次back();

window.history.go(1)--前进1页，相当于调用forward();

可以通过window.history.length，得到历史记录栈中一共有多少页。

### history.pushState()

h5中提供了不修改页面内容只修改地址栏的api。分别是pushState、replaceState、popState

pushState(添加浏览历史),replaceState(修改当前浏览历史)，popState事件在用户返回或前进进会被出发触发

history.pushState方法接受三个参数，依次为：

state：一个与指定网址相关的状态对象，popstate事件触发时，该对象会传入回调函数。如果不需要这个对象，此处可以填null。可用它来传一些数据。可以使用 history.state 读取其中的内容

title：新页面的标题，但是所有浏览器目前都忽略这个值，因此这里可以填null。

url：新的网址，必须与当前页面处在同一个域。浏览器的地址栏将显示这个网址。也可以直接写后缀不用写全地址。

history.replaceState方法跟pushState一样只不过replaceState是修改当前的状态

### 如何使用
```
window.history.pushState(state,null, url);
```
比如点击按钮给url上添加一个参数，并传入数据

```
<button id='btn'>按钮</button>
<script type="text/javascript">
    window.onload=function(){
        var oBtn = document.getElementById('btn')
        oBtn.onclick=function(){
            history.pushState({a:'传入数据'}, null, '?edit');//点击按钮后，此时url后面就会添加?edit这个参数。
        }
    }
</script>
```

### popState事件

通过此事件可以获得state的值。

仅仅调用pushState方法或replaceState方法 ，并不会触发该事件，只有用户点击浏览器倒退按钮和前进按钮，或者使用JavaScript调用back、forward、go方法时才会触发。另外，该事件只针对同一个文档，如果浏览历史的切换，导致加载不同的文档，该事件也不会触发。

此事件可以添加一个回调函数，函数的第一个参数为事件,事件的state属性是pushState和replaceState中传递的第一个参数state

还是上面的例子

```
<button id='btn'>按钮</button>
<script type="text/javascript">
    window.onload=function(){
        var oBtn = document.getElementById('btn')
        oBtn.onclick=function(){
            history.pushState({a:'传入数据'}, null, '?edit');//点击按钮后，此时url后面就会添加?edit这个参数。
        }
       //当点击浏览器的返回时，state为空，event.state为null,url为location.href
       //当点击前进时，event.state的值为{a:'传入数据'}，url为location.href+'?edit'

        window.addEventListener('popstate', function(event) {
            console.log('location: ' + document.location);
            console.log(event.state);
        });
    }
</script>
```

也就是说popstate事件发生时event里是和当前url匹配的一些数据

window还提供了onpopstate事件，也可以达到同样的效果

```
<button id='btn'>按钮</button>
<script type="text/javascript">
    window.onload=function(){
        var oBtn = document.getElementById('btn')
        oBtn.onclick=function(){
            history.pushState({a:'传入数据'}, null, '?edit');//点击按钮后，此时url后面就会添加?edit这个参数。
        }
       //当点击浏览器的返回时，state为空，event.state为null,url为location.href
       //当点击前进时，event.state的值为{a:'传入数据'}，url为location.href+'?edit'

        window.addEventListener('popstate', function(event) {
            console.log('location: ' + document.location);
            console.log(event.state);
        });

        //window.onpopstate = function(event) {
            //console.log('location: ' + document.location);
            //console.log(event.state);
        //};
    }
</script>
```

### window.history.state

Popstate 事件参数包含来自 pushState 或 replaceState 方法的状态对象（由数据参数设置）。

history.state 属性也包含状态对象。初次加载页面时，状态将为 Null。调用 pushState 时会清除前进堆栈，但调用 replaceState 不会清除前进堆栈。

如果把地址换了一个你想去的地址,就形成了一个简单的网页劫持

### window.history.replaceState()

用法和pushState类似，只不过是修改当前history的实体。