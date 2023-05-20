
#### Teleport: 传送门
```jsx
<template>
    <div class="box">哈哈哈</div>
    {/* 传送门：把视图中指定的内容，插入到除#app以外的容器中 */}
    <Teleport to="body">
        <div class="aside">呵呵呵</div>
    </Teleport>
</template>
```

#### Suspense 异步组件
等待一个组件的异步处理，当异步结束后，再渲染组件：使组件只需要渲染一次，就可以呈现真实的数据；在等待期间，展示#fallback中的信息！
组件由原来的调用两次(第一次掉用发送请求，第二次渲染数据)变为掉用一次(等待组件内请求返回后调用一次)

```jsx
// DomeThree.vue
<script setup>
import API from "@/api"
let list = await API.queryList()
</script>
<script>
import API from "@/api"
import {defineAsyncComponent} from "vue"
export default defineAsyncComponent{
    async setup(){
        let list = await API.queryList()
        return {
            list
        }
    }
}
</script>

<template>
    <p class="skeleton">我是骨架屏</p>
    <ul class="list">
        <li v-for="(item,index)in list" :key="index">{{item}}</li>
    </ul>
</template>
```
直接调用异步组件，什么都不会渲染，需要`<Suspense>`的配合
+ 直接在内部调用异步组件「此组件只需要“渲染一次”，就可以
看到真实的数据了」
+ #allback 是等待异步组件渲染之前，呈现的信息

```jsx
// app.vue
<template>
    <Suspense>
        <DomeThree />
        <template #fallback>
            我是骨架屏
        </template>
    </Suspense>
</template>
```
###### 非 setup 脚本模式创建异步组件的方法
```jsx
<script>
import API from "@/api"
export default {
    async setup(){
        let list=await API.queryList()
        return{
            list
        }
    }
}
</script>
```
#### Vue3的新特性
##### @1. 性能上的提升
###### Performance
+ 重写了虚拟DOM的实现（跳过静态节点，只处理动态节点）
    > vue2是基于 vue-template-compiler 实现对`<template>`视图的编译!
    > + 不论是静态还是动态节点，都需要进行编译处理
    > + 我们可以基于 v-pre/v-once 进行优化
    >
    > vue3是基于 @vue/compiler-sfc 实现对`<template>`视图的编译！
    > + 跳过静态节点，只处理动态节点
+ updata性能提高1.3~2倍
+ SSR速度提高了2~3倍
###### Tree shaking 
Vue的按需打包，降低打包后的文件大小
###### Proxy
+ 响应式原理不在基于 Object.defineProperty
+ https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy

##### @2. 功能更加强大了
Teleport
Suspense
更好的支持TS
Custom Renderer API
+ 自定义渲染器API
+ 用户可以尝试WebGL自定义渲染器

##### @3 语法上的变革
Vue3几乎完全支持Vue2语法「尤其是template视图构建」！但也有完全区别于Vue2的新语法！

+ Fragment 视图中支持出现多个根节点









```js
// global.js
import './assets/reset.min.css'
import {Button} from 'ant-design-vue'

export default function global(app){
    /* 全局注册 */
    app.component(Button.name, Button)

    /* 全局自定义指令 */
    // app.directive()

    /* 使用插件 */
    // app.use({
    //     install(app){}
    // })



    /* 全局混入「适配Vue2语法」 */
    app.mixin({})
    /* 全局配置「适配Vue2语法，代替Vue.prototype」 */
    app.config.globalProperties = {
        $API:API,
        $message:message
    }
}
```

```jsx

```


全局的相关操作
Vue2中是基于 Vue.xxx进行处理的
Vue3中是先基于createApp 创建app应用，基于 app.xxx 来实现全局相关操作的处理



Vue2和Vue3中的`<template>`构建视图的语法，几乎是一摸一样的
+ Vue3允许出现过个根节点
+ Vue3视图编译做了优化「跳过静态节点」
+ 关于v-for和v-if优先级变了「不论哪个版本，都不要吧v-for和v-if做用在相同的元素上」
    + Vue2: v-for的优先级高(可能出现创建后需要立即销毁，浪费性能)
    + Vue3: v-if的优先级高(在v-if中是无法使用v-for循环信息的)
+ ...



Vue3中采用 聚合式API 和 函数式编程
+ 聚合式API：把代码写在 setup 函数中「或者写在 `<script setup>`」
+ 函数式编程：我们从 vue 中解构出一个个的函数，来实现相关的需求
+ 视图构建：基于 `<template>` 和 jsx 语法


setup 函数会在“第一次”渲染组件的时候出发「组件更新此函数不执行」
+ 在 beforeCreate 之前触发：此函数中没有this(this->undefined)
+ 在 init props 之后触发：在函数中可以基于 形参props 获取注册接收的属性
    + 获取props对象是基于Proxy做代理劫持的
    + 而且它是 readonly 的 「只读」：可以基于 isReadonly 方法检测
+ 我们把需要创建的 状态、计算属性、监听器、钩子函数 等操作，都写在 setup 函数中
+ 在 setup 函数中，返回的信息，可以直接在视图中使用


基于 ref 函数创建状态
    let state = ref([initialvalue])
    + 获取的结果是RefImpl对象
    + 对象中具备 value 属性，存储了创建的状态值
    + value属性是响应式的：基于 Object.defineProperty 做的数据劫持
在JS代码中操作RefImpl对象
    xxx.value = 最新的值：设置新的值
    console.log(xxx.value):获取最新的值

基于 reactive 函数创建状态
    let state = reactive({...})
        + state是经过Proxy代理劫持后的

toRef/toRefs: 把state状态中的某一项/所有项，都变为 ObjectRefImpl 对象
    toRef(state, 'supNum') / toRefs(state)
    + 在setup函数中，我们最后返回状态给视图使用的时候，可以基于 toRefs 对状态进行处理，让其返回的都是RefImpl对象，这样方便在视图中使用@
    + 但在 `<script setup>` 脚本语法中，此操作意义不大


基于 watchEffect 创建监听器「Vue3新增」
    + 页面第一次渲染，watchEffect就会立即执行一次，并自动对其内部用到的状态建立监听机制「依赖」
    + 当依赖的状态发生改变，函数还会触发执行

基于 watch 函数创建监听器
    watch(state, handle) 监听所有的状态，任意状态发生变化，都会触发handle执行「不推荐」
    + 监听范围太广了，容易出现死循环
    + 如果真出现这样的需求(任意状态改变，做统一的事情)，我会把要做的事情放在onUpdated钩子函数中「但是也需要防止：不要在更新完毕后，再修改状态，否则也会出现死循环」
watch(()=> s)
    + 注意：监听某一个状态，需要把其写为函数
    + 但是如果监听的状态是基于 ref 创建的RefImpl对象


