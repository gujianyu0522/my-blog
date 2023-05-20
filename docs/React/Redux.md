# redux
## 一、使用
1. redux工程化
2. react-redux



## 二、使用详情
### redux 工程化
![Alt text](redux%E5%B7%A5%E7%A8%8B%E5%8C%96.png)

#### 1.创建Redux store
使用createStore函数创建Redux store。可以将其放在一个独立的文件中，例如store.js。


```js
// store/index.js
import { createStore } from 'redux';
import rootReducer from './reducers';

const store = createStore(rootReducer);

export default store;
```
#### 2.定义Action-Types
/store/action-types.js
```js
/* 派发行为标识 */
export const VOTE_SUP = 'VOTE_SUP';
export const VOTE_OPP = 'VOTE_OPP';
```

#### 3.定义Reducer
用于管理store中的数据。可以将reducer拆分为多个文件，最后使用combineReducers函数将它们合并。
```js
// store/reducers/voteReducer.js
import { VOTE_SUP, VOTE_OPP } from '../action-types';
import _ from '@/assets/utils';
let initialState = {
    supNum: 10,
    oppNum: 5
};
export default function voteReducer(state = initialState, action) {
    state = _.clone(true, state);
    let { type, payload = 1 } = action;
    switch (type) {
        case VOTE_SUP:
            state.supNum += payload;
            break;
        case VOTE_OPP:
            state.oppNum += payload;
            break;
        default:
    }
    return state;
};
```
```js
// store/reducers/index.js
import { combineReducers } from 'redux';
import voteReducer from './voteReducer';

const rootReducer = combineReducers({
    vote: voteReducer
});
export default rootReducer;
```
#### 4.创建Actions
定义一个或多个action creator函数，用于创建action对象。
```js
// store/actions/index.js
import voteAction from "./voteAction";
const actions = {
    vote: voteAction
};
export default actions;
```
```js
// store/actions/voteAction.js
import { VOTE_SUP, VOTE_OPP } from '../action-types';
const voteAction = {
    support(payload) {
        return {
            type: VOTE_SUP,
            payload
        };
    },
    oppose() {
        return {
            type: VOTE_OPP
        };
    }
};
export default voteAction;
```

#### ~~5.在组件中使用~~
```js
// 获取指定模块的状态
let { supNum, oppNum } = store.getState().vote;

// 派发任务的时候
import actions from '@/store/actions';
...
store.dispatch(actions.vote.support(10));
store.dispatch(actions.vote.oppose());
```

### react-redux

#### 1.`<Provider>`:把store注册到上下文中
在应用程序中导入store和根组件，并将store传递给`<Provider>`组件。
```jsx
// index.jsx
import store from './store';
import { Provider } from 'react-redux';

root.render(
  <Provider store={store}>
    <Vote />
  </Provider>
);
```
#### 2. connect:把公共状态和派发任务当做属性传递给属性

```jsx
import actions from '@/store/actions';
import { connect } from 'react-redux';
const VoteFooter = function VoteFooter(props) {
    let { supNum, oppNum, support, oppose } = props;
    return <div className="footer">
        <button onClick={support}>支持</button>
        <button onClick={oppose}>反对</button>
    </div>;
};
export default connect(
    state=>state.vote,
    actions.vote
)(VoteFooter);
```


### 原始使用方法
#### 1.创建store
```js
// store/index.js
import { createStore } from 'redux';
import _ from '@/assets/utils';

// REDUCER
let initial = {
    supNum: 0,
    oppNum: 0
};
const reducer = function reducer(state = initial, action) {
    //防止直接操作原始状态对象向
    state = _.clone(true, state);
    let { type, payload = 1 } = action;
    switch (type) {
        case 'VOTE_SUP':
            state.supNum += payload;
            break;
        case 'VOTE_OPP':
            state.oppNum += payload;
            break;
        default:
    }
    return state;
};

// 创建容器
const store = createStore(reducer);
export default store;
```
#### 2.创建全局上下文对象
```js
// ThemeContext.js
import { createContext } from 'react';
const ThemeContext = createContext();
export default ThemeContext;
```

```js
// index.js
...
import ThemeContext from './ThemeContext';
import store from './store';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <ThemeContext.Provider
    value={{
      store
    }}>
    <Vote />
  </ThemeContext.Provider>
);
```
#### 3.在组件中使用

## 三、API 参考
### combineReducers
#### 源码参考
```js
export default function combineReducers(reducers) {
  const reducerKeys = Reflect.ownKeys(reducers);
  return function combination(state = {}, action) {
    const nextState = {};
    for (let i = 0; i < reducerKeys.length; i++) {
      const key = reducerKeys[i],
        reducer = reducerKeys[key];
      const nextStateForKey = reducer(state[key], action);
      nextState[key] = nextStateForKey;
    }
    return nextState;
  }
}
```
### `createContext()`用于创建一个全局的上下文对象
是 React 的一个 API，用于创建一个全局的上下文对象，可以在 React 组件树中传递数据，使得组件之间可以通过上下文对象来共享数据。

createContext() 方法接受一个初始值作为参数，返回一个包含两个属性的对象：Provider 和 Consumer。

Provider 组件是数据的提供者，它需要通过 value 属性来设置数据，然后将需要使用这些数据的子组件包裹在 Provider 组件内部。这样，子组件就可以通过 Consumer 组件来获取 Provider 组件中设置的数据。

以下是一个示例，展示如何使用 createContext() 方法来创建上下文对象并在组件之间共享数据：

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


### `connect()`:使组件能够读取和修改store中的数据
在React组件中使用connect函数连接store和组件，使组件能够读取和修改store中的数据。可以将它们放在一个单独的文件中，例如Counter.js。
+ 自动获取上下文中的store
+ 自动把“让组件更新的方法”注册到store事件池中
+ mapStateToProps
+ mapDispatchToProps
```jsx
export default connect(
    null,
    dispatch => {
        return {
            support() {
                dispatch(actions.vote.support(10));
            },
            oppose() {
                dispatch(actions.vote.oppose());
            }
        }
    }
)(VoteFooter);
```

## 四、其它参考
