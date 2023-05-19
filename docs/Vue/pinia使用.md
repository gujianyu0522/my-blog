# Pinia.

Pinia 是一个基于 Vue3 的状态管理库，它的设计思想是简单、直观且易于使用。



- Vue3 和 Vue2的好处是什么？ CompositionApi（方便组合，高内聚低耦合） , OptionsApi(反复横跳)， 只编译导入进来的东西 Tree-shaking。  Pinia支持CompositionApi (也支持OptionsApi)
- Vuex缺点 区分mutation和 action。 
  - state -> component-> commit -> mutation
  - state -> component ->（ dispatch -> action ）-> commit -> mutation
  - state -> component(发送请求) ->  commit -> mutation  （action本质的目的就是为了让多个组件可以共享一些方法）
- Vuex缺点 modules. 嵌套问题. $store.state.a 模块和根状态命名冲突 （扁平化）
- Vuex（new Vue）需要依赖vue. 导致无法单独使用  单store （单例模式）
- Namespaced:true. pinia 里面完全解决了模块

- 极其轻巧，完整的 Typescript 支持；
- 去除 mutations，只有 state，getters，actions， 辅助方法 mapState
- 没有模块嵌套，只有 store 的概念，store 之间可以自由使用
- [支持 Vue devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)

## 一.基本使用

```bash
 pnpm create vite
 pnpm install pinia
```

```js
// 使用pinia
import { createPinia } from 'pinia'
const pinia = createPinia();
const app = createApp(App)
app.use(pinia)
app.mount('#app')
```

### 1.创建counterStore

**src/stores/counter.js**

```js
export const useCounterStore = defineStore('counter', {
    state: () => ({
        count: 0,
    }),
    getters: {
        double() {
            return this.count * 2
        }
    },
    actions: {
        increment() {
            this.count++
        },
        decrement() {
            this.count--
        },
    },
})
```

```vue
<script lang="ts" setup>
import { useCounterStore } from "./stores/counter.js";
const counter = useCounterStore();
</script>
<template>
 	<div>计时器 {{ counter.count }} {{ counter.double }}</div>
  <button @click="counter.increment">+</button>
  <button @click="counter.decrement">-</button>
</template>
```

### 2.创建todoStore

```js
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'
export const useTodoStore = defineStore('todo', () => {
    const todos = ref([]);
    // 完成的
    const completedTodos = computed(() => {
        return todos.value.filter((todo) => todo.completed)
    })
    // 未完成的
    const incompleteTodos = computed(() => {
        return todos.value.filter((todo) => !todo.completed)
    })
    // 添加
    const addTodo = (text) => {
        todos.value.push({
            id: Date.now(),
            text,
            completed: false,
        })
    }
    // 删除
    const removeTodo = (id) => {
        todos.value = todos.value.filter((todo) => todo.id !== id)
    }
    // 删除已完成
    const clearCompleted = () => {
        todos.value = todos.value.filter((todo) => !todo.completed)
    }
    return {
        todos,
        completedTodos,
        incompleteTodos,
        addTodo,
        removeTodo,
        clearCompleted
    }
})
```

```vue
<script setup>
import { ref, computed } from "vue";
import { useTodoStore } from "./stores/todo.js";
const newTodo = ref("");
const todoStore = useTodoStore();
const todos = computed(() => todoStore.todos);
const completedCount = computed(() => todoStore.completedTodos.length);
const incompleteCount = computed(() => todoStore.incompleteTodos.length);
function addTodo() {
  if (!newTodo.value.trim()) {
    return;
  }
  todoStore.addTodo(newTodo.value);
  newTodo.value = "";
}
</script>

<template>
  <div>计时器 {{ counter.count }} {{ counter.double }}</div>
  <button @click="counter.increment">+</button>
  <button @click="counter.decrement">-</button>
  <hr />
  <div>Todos</div>
  <input v-model="newTodo" />
  <button @click="addTodo">添加</button>
  <ul>
    <li v-for="todo in todos" :key="todo.id">
      <input type="checkbox" v-model="todo.completed" />
      {{ todo.text }}
      <button @click="todoStore.removeTodo(todo.id)">删除</button>
    </li>
  </ul>
  <span>{{ incompleteCount }} 未完成</span>
  <button @click="todoStore.clearCompleted" v-show="completedCount">
    删除完成事项
  </button>
</template>
```

### 3.$patch方法

```js
increment() {
  this.$patch({ count: 100 })
  this.$patch((state) => {
    state.count++
    state.count++
  })
}
```

### 4.$reset方法

```vue
<button @click="counter.$reset()">重置</button>
```

> 此方法只能在optionsAPI中使用

### 5.persists插件实现

```js
const persists = function ({ store }) {
    let state = localStorage.getItem(store.$id);
    if (state) {
        store.$state = JSON.parse(state)
    }
    store.$subscribe((mutation, state) => {
        localStorage.setItem(store.$id, JSON.stringify(state))
    })
}
pinia.use(persists)
```

### 6.持久化插件

**[pinia-plugin-persistedstate](https://github.com/prazdevs/pinia-plugin-persistedstate)**

```
pnpm i pinia-plugin-persistedstate
```

```js
import piniaPluginPersistedstate from "pinia-plugin-persistedstate";
const pinia = createPinia();
const app = createApp(App)
pinia.use(piniaPluginPersistedstate)
app.use(pinia)
```

```js
{
    persist: {
        storage: window.sessionStorage,
        paths: ['todos'], // 增加持久化配置
    },
}
```

