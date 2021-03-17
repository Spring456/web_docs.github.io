mixins 意思是混入，将一些公用的常用数据或者方法，构建一个可被混入的数据结构，放入mixin中，被不同的vue组件进行合并，就可以在不同的vue组件中使用相同的方法或者基础数据。可以让组件显得不再臃肿，提高了代码的复用性。

我们可以将 mixins 理解成一个数组，数组中有单或多个 mixin，mixin 的本质就是一个 JS 对象，它可以有 data、created、methods 等等 vue 实例中拥有的所有属性，甚至可以在 mixins 中再次嵌套。

### mixins什么时候使用
如果只是提取公用的数据或者通用的方法，并且这些数据或方法不需要组件间来维护，就可以使用mixins，类似于js中封装的一些公用的方法

使用场景：请求方式，时间格式。这些如果在用到的页面使用的话代码会重复的很多，所以在全局混入这些实例会减少代码量，可维护性也比较高。

### 使用mixins
```
const myMinxin = {
  data(){
    return{
      str:'这是一个混入'
    }
  },
  created(){
    this.getData()
  },
  methods:{
    getData(){
      console.log('这是混入的方法')
    }
  }
};
export default myMinxin
```
在另一个组件里引用：
```
import myMixin from '../components/mixin'

mixins:[myMixin],
```
页面中使用
```
<!--页面上就会渲染mixin混入来的字段-->
<p>{{str}}</p>
```

## 使用mixins需要注意的几点
### (1)、当组件和混合对象含有同名选项时,选项会按照规则进行合并
```
const myMixin = {
  data(){
    return{
      str:'这是一个混入'
    }
  },
  created(){
    console.log('这是混入方法的初始化')
  },
  methods:{
  }
};
export default myMixin
```
其他组件使用
```
<script>
import Logo from '~/components/Logo.vue'
import myMixin from "../components/myMiXin";
export default {
  components: {
    Logo
  },
  data(){
    return{
      str:'这是父组件'
    }
  },
  created() {
    console.log('这是父组件的初始化数据');
  },
  mounted() {
  },
  mixins:[myMixin],
  methods:{
  }
}
</script>
```

分别打印的是：=》这是混入方法的初始化
                       =》这是父组件的初始化数据
我们看到**混合对象的生命周期会优先被调用**。
### （2）、如果组件和混入有同样的属性和方法，会被组件的属性和方法覆盖
```
const myMixin = {
  data(){
    return{
      str:'这是一个混入'
    }
  },
  created(){
    this.getData();
  },
  methods:{
    getData(){
      console.log('这是混入的方法')
    }
  }
};
export default myMixin
```
```
<script>
import Logo from '~/components/Logo.vue'
import myMixin from "../components/myMiXin";
export default {
  components: {
    Logo
  },
  data(){
    return{
      str:'这是父组件'//会覆盖混入的str
    }
  },
  created() {
    this.getData();
  },
  mounted() {
  },
  mixins:[myMixin],
  methods:{
    getData(){
      console.log('这是组件的方法')
    }
  }
}
```
打印：=》这是组件的方法
         =》这是组件的方法
可以发现，当组件和混入有同样的属性或同样的方法，并且方法被调用的时候，组件会将这些与混入同样的属性和方法覆盖掉。但是调用方法并不会覆盖，只不过调用的是组件里面的方法。所以我们发现，组件里面虽然只调用了一次getData()，但是结果打印出来两个。也就是混入的方法也调用了组件的方法。