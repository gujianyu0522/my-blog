# React

## API 详情
### HOOK组件
#### useContext：在函数组件中访问上下文对象
useContext 是 React 提供的一个 Hook 函数，用于在函数组件中访问上下文对象。它接收一个上下文对象作为参数，返回该上下文对象的当前值。
```js
import React, { createContext, useContext } from 'react';

// 创建上下文对象
const MyContext = createContext();

// 提供数据的组件
function ParentComponent() {
  return (
    <MyContext.Provider value="Hello World">
      <ChildComponent />
    </MyContext.Provider>
  );
}

// 使用数据的组件
function ChildComponent() {
  // 使用 useContext 钩子函数获取 MyContext 上下文对象中的数据
  const data = useContext(MyContext);
  return <div>{data}</div>;
}

// 渲染 ParentComponent 组件
ReactDOM.render(<ParentComponent />, document.getElementById('root'));

```