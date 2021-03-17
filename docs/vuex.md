总结官方文档，方便查阅

## 什么是vuex
官方解释：专为vue.js开发的状态管理模式，把不同组件的共享状态抽取出来，以一个全局单例模式管理。状态就是数据，事件，属性等需要共享给其他组件使用的部分。

涉及到具体操作，也就是我们将事件数据抽离出来在别的文件中执行，在需要的地方引用。

## 核心概念
### store(仓库)
基本就是一个容器，它包含着你的应用中大部分的状态(state)。
vuex和单纯的全局对象有以下两点不同：
1.Vuex的状态存储是响应式的。store中的状态改变，组件中也会更新。
2.不能直接改变store中的状态。【应该提交mutation】
```
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```
使用 **store.state** 获取状态对象，通过 **store.commit**方法触发状态变更
```
store.commit('increment')
console.log(store.state.count) // -> 1
```
为了在Vue组件中访问 this.$store property,需要为Vue实例提供创建好的store,需要在根组件注入store机制：
```
new Vue({
  el: '#app',
  store: store,
})
```
Vuex的核心就是仓库store，这个store实例会被注入到所有子组件里面，里面的state属性保存着我们的状态,且子组件能通过 this.$store 访问到。然后可以从组件的方法提交一个变更:
```
methods: {
  increment() {
    this.$store.commit('increment')
    console.log(this.$store.state.count)
  }
}
```
## vuex有哪些属性？
五种，分别是 State、Getter、Mutation 、Action、 Module

### State
store(仓库)实例会注入到所有子组件中，里面的state属性保存着我们的状态，我们可以通过this.$store.state访问到。
我们定义一个count
```
export default new Vuex.Store({
  state: {
    count: 10
  },
})
```
这样就定义了一个集中管理的状态count，我们可以在组件中通过事件或者计算属性获取到count
```
export default {
  data() {
    
  },
  computed: {
    count() {
      return this.$store.state.count;
    }
  }
}
```

#### mapState
上面只是定义了一个状态count，如果需要获取多个状态，但是使用计算属性会调用多次，显得麻烦，就需要借助mapState方法来获取state。 使用mapState需要引入该方法
```
import { mapState } from 'vuex';
```
>注意：这里使用了mapState方法后，computed的写法有点区别，比如默认情况你的computed属性是这样写的：
```
data(){
  return{
    msg: 'hello '
  }
}
computed: {
  msg() {
    return this.msg + 'world!';
  }
}
```
那么你使用了mapState后需要这样写computed，把msg()放入mapState，不然会报错。
```
data(){
  return{
    msg: 'hello ',
    localCount: 20
  }
}
computed: mapState({
  msg() {  // 最初的
    return this.msg + 'world!';
  },
  // 使用mapState从store中引入state
  count(state) {
    return state.count;
  },
  name(state) {
    return state.firstName + ' ' + state.lastName;
  },
  mixCount(state) { // 结合store和组件状态进行计算
    return state.count + this.localCount;
  },
})
```
>如果你使用了展开运算符...，那么computed属性不需要改造，按正常写法写
```
computed: { // 使用展开的话可以按这种方式写，否则要使用另外一种方式，不然要报错
  msg() {
    return this.$store.state.msg;
  },
  // 这里返回一个状态count，
  // 返回多个你可以这样写...mapState(['count', 'firstName', 'lastName'])
  ...mapState(['count'])
},
```
### Getter
vueX允许我们在store中定义'getter'(可以认为是store中的计算属性)。有缓存，只有依赖值发生变化才会重新计算，我们可以在getter里对数据进行处理后，后再返回给组件使用，比如我们在state部分定义了一个list数组：
```
export default new Vuex.Store({
  state: {
    list: [1, 2, 3, 4, 5, 6, 7, 8]
  },
});
```
如果我们想将数组中的偶数提取出来给组件使用，那么这个操作可以在getter中完成。
```
export default new Vuex.Store({
  state: {
    list: [1, 2, 3, 4, 5, 6, 7, 8]
  },
  getters: { //  这个主要是对状态的处理，相当于把状态处理的方法抽成公共部分来管理了
    modifyArr(state) { // 一般化getter
      return state.list.filter((item, index, arr) => {
        return item % 2 == 0;
      })
    },
    getLength(state, getter) { // 方法里面传getter，调用modifyArr来计算长度
      return getter.modifyArr.length;
    }
});
```
之后再在其他组件的computed里面去调用getter来获取想要的状态
```
computed: {
    list() {
      return this.$store.getters.modifyArr;
    },
}
```
#### mapGetters
类似于mapState，当我们想在组件里引入多个getter，可以使用mapGetter
```
import {mapGetters} from 'vuex';
```
比如像刚才在在上面定义的modifyArr，getLength。我们想引入这个两个并获取其值：
```
computed: {
  ...mapGetter(['modifyArr', 'getLength'])
}
```
当然可以为其指定别名，不一定非得用store里面getters定义的名字：
```
computed: {
  mapGetter({
    arr: 'modifyArr',   // 把 `this.arr` 映射为 `this.$store.getters.modifyArr`,下面同理
    length: 'getLength'
  })
}
```
如果你的computed属性包含其他计算方法，那你就只能使用展开运算符的写法，这里跟mapState有点区别，其他计算属性如果写在mapGetter里面会报错，说不存在的getter，所以用以下的写法：
```
computed: {
  msg() {
    return this.num * 10;
  },
  ...mapGetters([
    'modifyArr',
    'getLength'
  ])
}
```
或者指定别名
```
computed: { 
  msg() {
    return this.num * 10;
  },
  ...mapGetters({
    getList: 'modifyArr',
    length: 'getLength'
  })
}
```
### Mutation
更改Vuex的store中的状态的唯一方法是提交mutation。【提交mutation可以方便管理，直接修改state中的状态过于分散，不好管理】
与Getter比较，Mutation是修改store里面的状态，组件通过事件来触发改变store里面的值；但是Getter相当于计算属性对数据进行处理后给组件使用。
在store里面有个count变量,分别使用add和reduce方法对count变量进行加减
```
const store = new Vuex.Store({
    state:{
        count:0
    },
    mutations:{
        add(state) {
            state.count++;
        },
        reduce(state) {
            state.count--;
        }
    }
})
```
在其他组件里，我们在methods里绑定事件来触发改变，这里需要用到commit
```
methods: {
  add() {
    this.$store.commit('add');
  },
  reduce() {
    this.$store.commit('reduce');
  }
}
```
#### 提交载荷
这个就是在commit时提交额外的参数，比如我传了额外的值加到count上面：
```
mutations: {
  loadAdd(state, payload) {  // 提交载荷，额外参数
    state.count += payload;
  }
},
```
然后再组件里面使用：
```
methods: {
  loadAdd() {
    this.$store.commit('loadAdd', 100); // 传递额外参数
  }
}
```
再这里官方文档建议载荷（也就是那个额外参数）最好使用对象来传，这样可以包含多个字段并且记录的 mutation 会更易读，像这样：
```
this.$store.commit('loadAdd', {
  extraCount: 100
}); // 传递额外参数
```
调用commit时我们也可以把所有参数写在一个对象里面：
```
this.$store.commit( {
  type: 'addLoad'
  extraCount: 100
}); // 传递额外参数
```
使用Mutation 需遵守 Vue 的响应规则
这个主要是说你再开发过程中需要向state里面添加额外数据时，需要遵循响应准则。
这里我直接照搬官方文档的说明：
Vuex 中的 mutation 也需要与使用 Vue 一样遵守一些注意事项：

最好提前在你的 store 中初始化好所有所需属性。

当需要在对象上添加新属性时，你应该使用 Vue.set(obj, 'newProp', 123), 或者以新对象替换老对象。例如，利用 stage-3 的对象展开运算符

我们可以这样写：
```
state.obj = { ...state.obj, newProp: 123 }
```
还是举个栗子： 我在mutation里面声明了一个方法
```
addNewState(state, payload) { // 我打算再这儿添加新的属性到state
  // Vue.set(state, 'newProp', '添加一个新值！'); // 这是一种写法
  // 这种写法用新对象替换老对象
  // state= {...state, newProp: '添加一个新值！'} // 这个玩意儿不管用了，用下面的replaceState()方法
  this.replaceState({...state, newProp: '添加一个新值！'})
}
```
然后再组件里面去调用，定义一个method：
```
addNewProp() {
  this.$store.commit('addNewState', {});
}
```
这样再执行了这个方法后，会及时更新到state，再组件的computed属性里面定义：
```
newMsg() {
  return this.$store.state.newProp || '还没添加新值';
}
```
在模板里面即时展示，并且不会影响其他状态：
```
<p>添加的新值：{{ newMsg }}</p>
<div><button @click="addNewProp">添加新值</button></div>
```

Mutation 必须是同步函数
下面这种写法必须避免（直接官方例子加持）：
```
mutations: {
  someMutation (state) {
    api.callAsyncMethod(() => {
      state.count++
    })
  }
}
```
#### mapMutations
这个跟前面的那几个函数一个道理，都是为了简化调用，使用方法如下：
```
import {mapMutations} from 'vuex';
```
然后在组件的methods里面使用，这里使用官方代码：
```

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```
### Action
Action 类似于 mutation，不同在于：

Action 提交的是 mutation，而不是直接变更状态。
Action 可以包含任意异步操作。
前面说过mutation只能包含同步事务，所以在处理异步事务就需要Action，通过Action控制了异步这一过程，之后再去调用mutation里面的方法来改变状态。
这里我直接贴代码来一目了然，首先我定义了一个状态product：

```
state: {
  product: 'car'
}
```
然后再mutation中定义一个方法：
```
changeProduct(state, payload) {
  state.product = payload.change;
}
```
在action中定义：
```
actions: {
  changeProduct(context, payload) { // 这个context是一个与 store 实例具有相同方法和属性的对象
    // 调用mutation里的changeProduct方法
    // context.commit('changeProduct', {change: 'ship'});
    // 改成异步方式
    // setTimeout(() => {
    //   context.commit('changeProduct', {change: 'ship'});
    // }, 1500)
    // 使用载荷
    let temp = 'ship+' + payload.extraInfo; 
    setTimeout(() => {
      context.commit('changeProduct', {change: temp});
    }, 1500)
  }
}
```
在组件methods中定义事件触发分发：
```
methods: {
  selectProduct() {
    // this.$store.dispatch('changeProduct')
    // 载荷方式分发
    // this.$store.dispatch('changeProduct', {
    //   extraInfo: 'sportcar'
    // })
    // 或者这种
    this.$store.dispatch({
      type: 'changeProduct',
      extraInfo: '->sportcar'
    })
  }
},
```
这样一个简易的action就完成了！
#### mapActions
这里就不再赘述了，看名字就知道跟前面几个叫map开头的辅助函数类似，用来映射action里面的方法，这里也直接贴官方代码了：
```
import {mapActions} from 'vuex';
```
```
export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```
有时候我们想知道action里面异步执行后的状态然后再去修改其他信息，这个可以借助Promise来实现。这里在state里面声明一个状态：
```
state: {
  userInfo: { // 这个变量用来测试组合变量
    name: 'lee',
    age: 23
  }
}
```
接着声明mutation:
```
mutations: {
    // 以下用来测试组合action
    changeInfo(state, payload) {
      state.userInfo.name = 'lee haha';
    }
}
```
声明action：
```
actions: {
  changeInfo(context, payload) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        context.commit('changeInfo');
        resolve();
      }, 2000)
    })
  }
}
```
这时我们在组件里面定义方法去派发这个action：
```
data() {
  return {
    status: '信息还没修改！'
  }
}
methods: {
  modifyInfo() {
    this.$store.dispatch('changeInfo').then(() => {
      this.status = '信息修改成功';
    });
  }
}
```
模板展示：
```
<template>
  <div>
    <h2>组合action</h2>
    <p>{{ status }}</p>
    <p>{{ info.name }}</p>
    <div><button @click="modifyInfo">修改信息</button></div>
  </div>
</template>
```
当我们点击修改信息后，就会派发action，当修改成功的时候会同步修改上面说的展示信息。 当然其他定义的action方法也可以互相使用，这里直接贴官方代码了：
```
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  },
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```

