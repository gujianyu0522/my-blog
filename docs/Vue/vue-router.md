# Vue-router
```
npm install vue-router
```
## 一、使用
### 1. 构建路由表
/routes/route.js
```js 
import Home from ?'/Home'
import About from ?'/About'

const routes = [{
    path: '/',
    name: 'home',
    meta: {},
    component: Home
},{
    path: '/about',
    name: 'about',
    meta: {},
    component: About
},{
    path: '*',
    name: 'error',
    meta: {},
    component: ErrorPage, 
    // redirect: '/' // 重定向到首页
},]
export default routes
```
### 2.创建路由实例
/routes/index.js
```js
import Vue from "vue";
import VueRouter from "vue-router";
import routes from "./routes";

Vue.use(VueRouter);

const router = new VueRouter({
    // 指定路由的模式：默认hash（哈希路由）、history（History路由）
    mode: 'hash',
    // 指定路由表
    routes
});

export default router;
```
### 3.将路由实例添加到Vue实例中
/main.js
```js
new Vue({
    router,
    render: h => h(App)
}).$mount('#app')
```
### 4.组件中使用
```jsx
<template>
    <div>
        <div class='nav-box'>
            <router-link to="/">首页</router-link>
            <router-link to="/about">关于</router-link>
        </div>

        <router-view />
    </div>
</template>
```
## 二、使用详情

## 三、API 参考
### routes: 构建项目需要的路由表
#### 语法
```js
interface RouteConfig = {
  path: string,
  component?: Component,
  name?: string,
  redirect?: string | Location | Function, 
  props?: boolean | Object | Function, 
  children?: Array<RouteConfig>, 
  beforeEnter?: (to: Route, from: Route, next:  Function) => void, 
  meta?: any, 
  components?: { [name: string]: Component }, 
  alias?: string | Array<string>,
  // 2.6.0+
  caseSensitive?: boolean, 
  pathToRegexpOptions?: Object
}
```
##### path: 匹配的路由地址
##### component: 路由匹配后需要渲染的组件
##### name: 命名路由
##### redirect: 路由重定向
##### props: 路由匹配后，给需要渲染的组件传递属性
`props:{ x:100,y:200 }`
如果 props 被设置为 true，则会自动将路由参数注入到组件的 props 属性中
还可以将其设置为一个函数，以便在注入路由参数时进行更复杂的处理

##### children: 嵌套路由(子路由)
```js
const routes = [
  {
    path: '/user',
    component: User,
    children: [
      {
        path: 'profile',
        component: Profile
      },
      {
        path: 'posts',
        component: Posts
      }
    ]
  }
]
```
> 需要注意的是，当定义嵌套路由时，父级路由的组件中必须包含一个 `<router-view>` 标签，用于渲染子路由的组件。
> 嵌套路由的子路由可以是相对路径或绝对路径。示例中，子路由的路径是相对于父级路由的，如果要定义一个绝对路径的子路由，可以使用 / 开头的路径。
##### beforeEnter: 路由独享守卫函数
##### meta: 存储与路由相关的元数据「路由元信息」
当每一次路由切换的时候，拿到这个元信息，然后基于不同的值做不同的事情
`meta:{ requiresAuth: true,title:"home" }`

> 建议每个匹配项中都设置对应的字段。
##### components: 命名视图
##### alias: 
##### caseSensitive: 匹配规则是否大小写敏感(默认值: false)「2.6.0+」
##### pathToRegexpOptions: 编译正则的选项「2.6.0+」



### `<router-link>`:创建链接组件「切换路由」
```jsx
<router-link to="/home">Home</router-link>
```
+ router-link 经过视图编译，最后会渲染为`<a>`标签
    哈希路由：`<a>`标签的 href 值是跳转地址的哈希值，点击a标签实现哈希值的切换
#### 参数
##### to:指定链接的目标路由。
to 的值可以是一个字符串，也可以是一个对象「必须设置」

###### **path**：指定链接的目标路由路径。
`<router-link to="/about">`
###### **name**：指定链接的目标路由名称。
`<router-link to="{ name: 'user', params: { id: 1 } }">`
###### **params**：指定链接的动态路由参数。
`<router-link to="{ name: 'user', params: { id: 1 } }">`
###### **query**：指定链接的查询参数。
`<router-link to="{ path: '/search', query: { q: 'vue' } }">`
###### **hash**：指定链接的哈希值。
`<router-link to="/#hash">`
###### **replace**：是否使用 router.replace() 方法替换当前路由。
设置这个属性后，每一次跳转都不会新增一条历史记录
`<router-link to="/about" replace>`
###### **append**：是否将链接添加到当前路由之后。
`<router-link to="/about" append>`
###### **exact**：表示是否精确匹配路由。
`<router-link to="/about" exact>`
###### **active-class**：用于指定当前激活链接的 CSS 类名。
`active-class="active"`



### Router实例方法:进行函数式路由跳转
#### `push`:跳转且新增历史记录
```js
this.$router.push(location, onComplete?,onAbort?)
```
##### location:与router-link中的to相同
可以是一个字符串，也可以是一个对象
```js
this.$router.push("/home");

this.$router.push({
    path:"home",
});
```

#### `replace`:跳转但是替换现有的历史记录
#### `go(n)`:跳转到指定索引对应的路由地址，不会新增历史记录
#### `back`:回退一步
--> go(-1)
#### `forward`:前进一步
--> go(1)

### 路由守卫

#### `afterEach`: 全局后置首位「在每一次路由“切换完毕”后出发
```js
router.beforeEach((to, from, next) => {

})
```
#### `beforeEach`全局前置首位「在每一次路由“切换完毕”前出发
必须调用 `next`
```js
router.beforeEach((to, from, next) => {
    
  // 获取当前路由对象的 requiresAuth 元数据属性，并判断用户是否已经登录
  if (to.meta.requiresAuth && !isAuthenticated()) {
    next('/login')
  } else {
    next()
  }
})
```
#### `beforeRouteLeave`组件内守卫
#### `afterRouteLeave`
#### `beforeRouteUpdate`组件内守卫
#### `beforeEnter`:路由守卫
#### ``
#### 详情
+ 全局守卫: 把钩子函数写在 router/index.js 中
+ 路由守卫: 把钩子函数写在路由表中
+ 组件内的守卫: 把钩子函数写在组件的OptionsAPI中

每个守卫方法接收三个参数：

##### to: 即将要进入的目标 路由对象

##### from: 当前导航正要离开的路由

##### next: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。

+ `next()`: 进行下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。
+ `next(false)`: 中断当前的导航。
如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。
+ `next('/')` 或 `next({ path: '/' })`: 跳转到一个新的地址。
你可以向 next 传递任意位置对象，且允许设置诸如 replace: true、name: 'home' 之类的选项以及任何用在 router-link 的 to prop 或 router.push 中的选项。
+ `next(error)`: (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调。

##### 完整的导航解析流程
1. 导航被触发。
2. 在失活的组件中调用beforeRouteLeave 守卫「组件内守卫」
3. 触发 beforeEach 守卫: 路由切换开始「全局守卫」
4. 组件重用触发 beforeRouteUpdate 守卫(2.2+)「组件内守卫」。
如果是从A组件跳转A组件，这相当于组件重用，此时触发。
5. 在路由表中逐一匹配，匹配到触发 beforeEnter 路由独享守卫「路由守卫」
6. 解析异步路由组件。
7. 在被激活的组件中触发 beforeRouteEnter 守卫「组件内的守卫」
8. 触发全局的 beforeResolve 守卫(2.5+)「全局守卫」。
导航被确认。
9. 触发全局后置守卫 afterEach: 路由切换完毕「全局守卫」
10. 触发 DOM 更新。
11. 触发 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。
### 路由对象

每一次路由跳转，都会对当前跳转的路由地址，进行匹配解析，以此来决定渲染哪个组件，以及把匹配解析的信息赋值给
`this.$route()`获取当前路由匹配的信息
+ path: 当前路由地址
+ fullPath: 当前路由最全的地址「包含问号传参等」
+ name: 我们再路由表中给当前路由设置的名字
+ meta: 当前匹配项的路由元信息「也是在路由表中设置的」
+ params/query: 路由跳转的时候，传递的信息
+ ...

而我们在项目中，偶尔也会这样处理
```js
watch:{
    $route(){
        // 监听路由跳转
    }
}
```



## 四、其它详情
### VueRouter的基本流程
![Alt text](VueRouter%E7%9A%84%E5%9F%BA%E6%9C%AC%E6%B5%81%E7%A8%8B.png)

### 路由跳转的流程
每一次路由跳转 -> 都会去路由表中进行匹配 -> 把匹配的内容放在路由容器中渲染

> 页面第一次加载的时候
> + 如果已经存在哈希值，则直接去路由表进行匹配
> + 如果没有哈希值，会根据所选的路由模式，自动在现有地址后面加一些内容("#/")
> 所以此时也相当于路由跳转了一次
> + 手动在地址栏中修改地址，也可以实现路由跳转

### 路由跳转的方式

1. 基于`<router-link to>`实现跳转
2. 编程式导航

### 路由跳转的传参方案
#### 1. Query 参数传递「问号传参」
使用 query 参数传递参数是最简单的方式，只需要在跳转时在 URL 后面拼接参数即可。例如：

```js
// 跳转到 /user 页面，并传递 id 和 name 参数
this.$router.push({ path: '/user', query: { id: 1, name: 'John' }})
```
基于 $route.query 接收传递的参数
```js
// 获取参数
this.$route.query.id
this.$route.query.name
```

#### 2. 动态路由传递参数「隐式传参」
使用动态路由传递参数需要在定义路由时添加参数占位符，例如：

```js
// 跳转到 /user/1 页面，并传递 id 参数
this.$router.push({ name: 'User', params: { id: 1 }})
```
基于 $route.params 接收传递的参数
```js
// 获取参数
this.$route.params
```

#### 3. 路径传参
把要传递的信息作为路径中的一部分，其只能基于“地址”进行跳转
```js
// 定义路由
{
  path: '/user/:id?/:name?',
  name: 'User',
  component: User
}

// 跳转到 /user/1 页面，并传递 id 参数
this.$router.push({ path: 'User/0'})
```
基于 $route.params 接收传递的参数
```js
// 获取参数
this.$route.params
```


### 路由懒加载
我们基于ES6中的 import 函数，实现路由懒加载
`component: () => import('@views/home.vue')`
+ @vue/cli 脚手架在打包的时候，会把动态导入的组件，单独打包到一个JS中！
+ 可以基于一个webpack能识别的注释，控制打包后的JS文件的名字！
+ 设置为相同名字的组件代码，会打包到一个JS中！
`component: () => import(/* webpackChunkName:"AAAAA" */ '@views/home.vue')`