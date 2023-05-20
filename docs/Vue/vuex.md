# Vuex
## 一、使用
### 1.创建 store
/store/index.js
```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    count: 0
  },
  getters: {
    doubleCount(state) {
      return state.count * 2;
    },
  },
  mutations: {
    increment(state) {
      state.count++
    }
  },
  actions: {
    incrementAsync({ commit }) {
      setTimeout(() => {
        commit('increment')
      }, 1000)
    }
  },
  modules:{
    a:{
        namespaced:true,
        state:{},
    }
  },
  plugins:{

  }
})

export default store;
```

### 2.在 Vue 应用程序中使用 store
/main.js
```js
import Vue from 'vue'
import store from './store'

new Vue({
  store,
  render: h => h(App)
}).$mount('#app')
```
### 3.在组件中使用 store
```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="incrementCount">Increment</button>
  </div>
</template>

<script>
export default {
  computed: {
    count() {
      return this.$store.state.count
    }
  },
  methods: {
    incrementCount() {
      this.$store.commit('increment')
    },
    incrementCountAsync() {
      this.$store.dispatch('incrementAsync')
    }
  }
}
</script>
```

## 二、使用详情

### Vuex的核心概念
```js
import Vuex from 'vuex'

const store = new Vuex.Store({ ...options })
```
#### State:公共状态信息 
State是一个对象，用于存储整个应用程序的状态。

> + 存储的公共状态是经过GET/SET劫持的
> + 和组件内在data中编写的状态，实现了类似的劫持方案
>
> State应该是唯一的数据源，它会被注入到所有的组件中，作为组件的data属性的一部分。

#### Getters:公共计算属性 
Getters用于获取state的值，并将其暴露给组件。

> + 也做了数据劫持，但是只有GET劫持
> + 和组件的computed类似，其具备“计算缓存”的效果
> + 但是不允许直接修改getters中的计算属性值，会报错的！！
>
> Getters类似于计算属性，可以接受其他的getter作为第二个参数。

#### Mutations:同步修改状态的方法
Mutations用于修改state的值。它们必须是同步的函数。每个mutation都有一个字符串类型的事件类型和一个回调函数，该回调函数将接收当前的state作为其第一个参数。
+ state:现有的公共状态信息{固定}
+ payload:通知方法执行传递进来的实参值；我们一般都把其设置为对象格式，因为基于commit通知其执行的时候，只支持传递一个实参，如果想传递多个值，则传递一个对象即可！
>「唯一可以修改状态的方法」



#### Actions:异步修改公共状态的方法
Actions类似于mutations，但它们可以包含异步操作。
Actions接收一个与context对象具有相同属性和方法的对象参数。
> 这意味着，您可以使用`context.commit`提交mutation、使用`context.dispatch`分发其他actions和访问state。
> 「在异步操作结束后，我们需要基于commit通知mutations中的方法执行」

#### Modules
Module允许您将store分割为更小的、可重用的模块。每个模块都有自己的state、getters、mutations和actions。
##### 公共状态
```js
$store.state -> {
    // root部分的状态
    num:10
    ...
    //模块部分的状态：按照模块名分别进行管理，防止相互冲突
    a:{
        x:0,
        ...
    },
    b:{
        ...
    }
}
```
当使用modules时总的公共计算属性变为
```js
$store.getters -> {
  // root部分计算属性
  newNum,
  ...
  // 各模块部分的计算属性是以 “模块名/计算属性名” 管理
  a/newX,
  b/xxx,
  ...
}
```

### 组件绑定的辅助函数

#### mapState
```js
import { mapState } from 'vuex'

export default {
  computed: {
    ...mapState(['count'])
  }
}
```
##### mapState 详情
mapState 接受一个数组作为参数，数组中可以是状态名称的字符串，也可以是一个包含计算属性名和状态名的对象。
```js
...mapState([
    // 将 `this.count` 映射为 `this.$store.state.count`
    'count'
    ])

...mapState({
    // 将 `this.count` 映射为 `this.$store.state.count`
    count: 'count',
    
    // 将 `this.otherCount` 映射为 `this.$store.state.otherCount`
    otherCount: 'other/count'
})
```

也还可以在对象中定义一个函数，以便在映射时执行更复杂的逻辑。函数将接受 Store 中的状态作为第一个参数

```js
...mapState({
    // 将 `this.fullName` 映射为 `this.$store.state.firstName + ' ' + this.$store.state.lastName`
    fullName(state) {
    return state.firstName + ' ' + state.lastName
    }
})
```

#### mapGetters

为组件创建计算属性以返回 getter 的返回值。

+ 第一个参数是可选的，可以是一个命名空间字符串。

#### mapActions

创建组件方法分发 action。

+ 第一个参数是可选的，可以是一个命名空间字符串。

+ 对象形式的第二个参数的成员可以是一个函数。

#### mapMutations

创建组件方法提交 mutation。

+ 第一个参数是可选的，可以是一个命名空间字符串。

+ 对象形式的第二个参数的成员可以是一个函数。






## 三、更多详情
Vuex：Vue框架生态体系中重要的组成部分，主要用来处理 “公共状态管理(实现复合组件之间的通信)、对不经常更新的数据进行缓存” 这些事情！和其类似的一套方案：pinia！

### 版本对应
+ Vue2 -> Vuex3
+ Vue3 -> Vuex4

### Vuex处理流程
![Alt text](Vuex%E7%9A%84%E5%A4%84%E7%90%86%E6%B5%81%E7%A8%8B%E3%80%8C%E5%AE%98%E6%96%B9%E3%80%8D.png)




 
  + 如果需要在组件中，使用公共的计算属性(getters)，也需要把其赋值给组件私有的计算属性


如何在组件中，通知 mutations/actions 中的方法执行？
  + 通知 mutations 中的方法执行：this.$store.commit('方法名',传参信息{建议是个对象})
  + 通知 actions 中的方法执行：this.$store.dispatch('方法名',传参信息{建议是个对象})


 




/* 
 在Vue2/Vuex3中，这行操作一定得有！！
 首先会调用 Vuex.install 方法，在 install 方法中
   + 基于 Vue.mixin({ beforeCreate: vuexInit }) , 向每个组件都混入一个 beforeCreate 钩子函数，这样每个组件渲染的时候，首先会执行这个钩子！
   + vuexInit 函数执行的目的：给每个组件都挂载一个 $store 的属性
     + 需要在根组件的配置项中，设置 store 属性，存储我们创建的store容器；这样处理后，根组件的实例上就挂载了 $store 属性！！
       new Vue({
         store: ...,
         render: h => h(App)
       }).$mount('#app')
     + 当其它组件再渲染的时候，不需要手动设置 store 配置项，直接会去其父组件的实例上找，并且给自己的实例，也挂载一个 $store 的属性！
 这行代码的最终目的是：只要保证根组件的配置项中，设置了store这个配置项，那么每个组件的实例上，都会挂载一个 $store 这个属性！！
 */
 ##### 公共状态改变，让使用其信息的组件更新
  + 把公共状态赋值给组件“私有”的计算属性：组件的私有计算属性依赖这个公共状态
    computed: {
        supNum() {
            return this.$store.state.supNum;
        },
        ....
    }
