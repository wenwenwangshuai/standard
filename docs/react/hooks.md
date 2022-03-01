# React Hooks

------------

* 有状态的组件没有渲染【 hook 函数组件返回值不是 DOM 元素】
* 有渲染的组件没有状态【 hook 函数组件没有 useState 存储状态值】

## 为什么使用hooks

1. useEffect 可以让相同逻辑在同一个地方进行处理【eg：事件的订阅与取消】

2. Hooks 体现了`React`在组件内部进行逻辑隔离，不像`class`组件的`state`可自定义和跨组件重用。

3. 可以将组件相同逻辑放置在自定义`hook`中使用【`class`组件是使用高阶组件 (HOC) 进行逻辑复用的】
   > `hooks`实现的是「状态细粒度划分」，「状态以及变更逻辑隔离」，更加体现了`web component`中的设计原则

## hooks的使用规则

1. 只能在`函数最外层`调用`hook`【不要在循环、条件判断或者子函数中调用】
   > 确保`Hook`在每一次渲染中都按照同样的顺序被调用。

2. 只能在`React 的函数组件`中调用`hook`【或者在`自定义 hook`中使用】
   > 确保组件的状态逻辑在代码中清晰可见。

[官方文档](https://react.docschina.org/docs/hooks-rules.html)

## 一、useState【状态钩】

```js
const [state, setState] = useState(initState) // 函数返回值state命名可以【任意】命名
```

1. 接受参数： state 的初始值
2. 返回值：当前 state 值及异步更新 state 的函数 setState( )

**说明：**

1. `state`只在`首次渲染`时被创建且赋予初始值`initState`【该初始值不仅仅局限于对象】
2. useState( ) 函数`可以定义多个`，用来保存相应的状态数据信息
3. 调用更新函数 setState( ) 时，函数组件将`重新渲染`，并赋予`state`最新的值

## 二、useEffect【效果钩(副作用函数)】

> **副作用操作**：数据获取，设置订阅以及手动更改 React 组件中的 DOM<br/>
> **无需清除的操作**：发送网络请求，手动变更 DOM，记录日志<br/>
> **需要清除的操作**：订阅外部数据源【防止引起内存泄漏】(见说明第2点)

```js
useEffect(() => {}, [param])
```

1. 接受参数：
* 参数1：执行操作逻辑函数
* 参数2：更新 effect 依赖项参数数组

2. 可选清除订阅机制：操作逻辑函数返回函数中执行清除操作（见下说明第2点）

**性能优化：【控制effect的执行（依赖项数组参数）】**

1. 当依赖项数组`未设置`时，默认每次渲染会更新后执行`effect`
2. 当依赖项参数数组`为空`时，只在初次渲染后执行`effect`
3. 当 effect 中依赖项参数数组中的值渲染时**未发生变化**时，则 effect 不会执行更新替换操作

**说明：**

1. 默认情况下，React 会在`每次渲染后调用`副作用函数 ——`包括`第一次渲染的时候【 DOM渲染-调用副作用函数-页面展示最新数据信息】
2. useEffect 可以通过`返回一个函数`来指定如何`清除`相关的副作用操作，便于将添加和移除订阅的逻辑放在一起
3. 每次我们重新渲染，默认都会生成`新的 effect`，替换掉之前的【避免因没有处理更新逻辑而导致常见的 bug】
4. effect 的清除阶段在`每次重新渲染时都会执行`，而不是只在卸载组件的时候执行一次

[参考链接](https://overreacted.io/zh-hans/a-complete-guide-to-useeffect/)

## 三、useContext【很多不同层级的组件需要访问同样一些的数据】

```js
import React from 'react';

const MyContext = React.createContext(defaultValue);

// 外层包裹组件
<MyContext.Provider value={initValue}>
  <MyComponents /> // 可以共享MyContext的数据
</MyContext.Provider>

// 函数子组件 MyComponents
import React { useContext } from 'react'

function MyComponents() {
  const contextData = useContext(MyContext) // 获取MyContext中的共享数据信息
}


1. 使用 useContext 结合 userReducer 为顶层组件构建一个全局store
// context.js
import React, { createContext } from 'react';

export const Context = createContext();

function reducer(state, action) {
  const { type, payload = {} } = action || {};
  switch(type) {
    case 'search':
      return { ...state, params: action.params };
    case 'update':
      return Object.assign({}, state, payload);
    default: 
      return state;
   }
}

export function ContextProvider (props) {
  const { state, dispatch } = userReducer(reducer, initialState);
   
  return(
    <Context.Provider value={ { state, dispatch } }>
      { props.children }
    </Context.Provider>
  )
}

// 使用的页面
import React, { useContext } from 'react';
import { ContextProvider } from 'context';

export default function MyPage() {
  return (
    <ContextProvider>
      <MyComponents />
    </ContextProvider>
  )
}

// 函数子组件 MyComponents
import React { useContext } from 'react';
import { Context } from 'context';

function MyComponents() {
  const { state, dispatch } = useContext(Context);
  
  // 更新 state 中的搜索参数 params
  handleSearch = () => {
    dispatch({
        type: 'update',
        params: {}
    })
  }
  
  // 更新 state 中的属性
  handleUpdate = () => {
    dispatch({
        type: 'update',
        payload: {}
    })
  }
  
  return (
    <>
      <button onClick={handleSearch}>search</button>
      <button onClick={handleUpdate}>update</button>
    </>
  )
}
```

**说明：**

1. `useContext(MyContext)`只可以帮助我们获取`context`值和订阅`context`的变化，仍然需要在上层组件中使用`Provider`来提供`context`, 并且多个`Provider`可以相互嵌套使用
2. 当 react 渲染订阅了`context`的组件时，该组件会从距离他`最近的 Provider`中获取`当前的 context【initValue】`值, 若没有匹配到相应的`Provider`则`defaultValue`生效
3. 将`undefined`传递给`Provider`的`value`时，消费组件的`defaulValue不会生效`
4. Provider 的`value`值发生变化时，它内部的所有消费组件都会`重新渲染`

> 注意：`value`值为对象时，当`provider`父组件重新渲染时【`value`被赋予一个`新的 object`】，可能会引起`consumer`消费子组件不必要的渲染。【状态提升至 `state`】

## 四、unstated-next【React轻量状态管理库】(结合 useReducer)

**基本使用：**

```js
/**
 * 1. 使用 createContainer 构建存储容器
 **/

import { createContainer } from "unstated-next"

// 定义一个reducer纯函数处理相应逻辑并更新 state
function reducer(state, action) {
  switch(action.type) {
    case 'add':
      return { ...state, propName: action.payload };
    default:
      return state;
  }
}


// 
function useLocalStore() {
  const [state, dispatch] = useReducer(reducer, initialState);
  
  return {
    state,
    dispatch
  }
}

export default createContainer(useLocalStore);


/**
 * 2. 需使用该 store.Provider 包裹需要使用 store 的使用的组件
 */
import Store from './filePathName';

<Store.Provider>
  <Components />  // 需要使用该轻量级仓库的组件
</Store.Provider>

/**
 * 3. 在需要的页面中使用
 **/
import Store from ‘./filePathName’;

const { state: { initCount }, dispatch } = Store.useContainer();

/**
 * 4. 解决多层container嵌套问题
 **/
<Container1.Provider>
  <Container2.Provider>
   <Container3.Provider>
    <MyApp />
   </Container3.Provider>
  </Container2.Provider>
</Container1.Provider>

// 修改为:
function compose(...containers) {
  return function Component(props) {
    return containers.reduceRight((children, Container) => {
      return <Container.Provider>{children}</Container.Provider>
    }, props.children)
  }
}

let Provider = compose(Container1, Container2, Container3);

<Provider>
  myApp
</Provider>

/**
 * 5. useReducer简单内部构建
 */
function useReducer(reducer, initialState) {
  const [state, setState] = useState(initialState);

  function dispatch(action) {
    const nextState = reducer(state, action);
  	setState(nextState);
  }
  return [state, dispatch];
}

useReducer(reducer, initialArg, init);
```

1. 指定初始state

```js
useReducer(reducer, { count: initCount });
```
* initialArg：容器库 initState 对象

2. 惰性初始化

```js
useReducer(reducer, initCount, function(initCount) { return {count: initCount} })
```

* initialArg：init 函数的参数值
* init 函数：可以用于对初始数据信息执行一些逻辑操作

```js
function reducer(state, action) {
  const { payload = {} } = action || {};
  return {
    ...state,
    ...payload,
  }
}

const [query, setQuery] = useReducer(reducer, {
  pageNo,
  pageSize,
  createAt,
  ...
})

// 使用时
setQuery({ payload: { ...query, pageNo:_pageNo } })
```

**说明：**

1. `React`会确保`dispatch`函数的标识是稳定的，并且不会在组件重新渲染时改变
2. 当`Reducer Hook`的返回值与当前`state`相同，`React`将跳过子组件的渲染及副作用的执行

[参考链接](https://www.jianshu.com/p/f5d0d777b523)

## 五、 useCallback（主要用于处理对于一些数据更新引起其他组件不必要的渲染）

```js
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

> useCallback(fn, deps) 相当于 useMemo(() => fn, deps)

1. 返回值：返回一个`memoized`回调函数

2. 接受参数：
* 参数1:：内联函数
* 参数2：依赖项数组

**说明：**

1. 只有当依赖项发生改变时，内联回调函数中的值才会得到最新值。否则，内联回调函数中的变量都是之前时侯的值。
2. 当传入空数组时，该返回的`memoized`回调函数一直不会发生改变。

[参考链接](https://segmentfault.com/a/1190000020108840)

## 六、useMemo【优化避免在每次渲染时都进行高开销的计算逻辑】

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

1. 返回值：返回一个 memoized 值

2. 接受参数：
* 参数1:：“创建”函数
* 参数2：依赖项数组

**说明：**

1. 若没有提供依赖项，`useMemo`在每次渲染时都会计算新的值
2. 若提供依赖项数组，当某个依赖项改变时才会重新计算`memoized`值
3. 不要在该“创建”函数内部执行与渲染无关的操作【如副操作等适合使用在其它`hook`中的逻辑】

## 七、useRef

```js
const refContainer = useRef(initialValue);
```

1. 返回值：返回一个可变的`ref`对象，返回的该`ref`对象在组件的整个生命周期内保持不变
2. 返回可变的`ref`对象的`current`属性初始化值为`useRef`传入的参数值`initialValue`

**作用：**

* 获取组件实例对象或 DOM 对象
* 可以`跨渲染周期`保存数据

**说明：**

1. 当`ref`对象内容发生变化时，`useRef`并不会通知你
2. 变更`.current`属性不会引发组件重新渲染
3. 想要在`React`绑定或解绑 DOM 节点的`ref`时运行某些代码，则需要使用回调`ref`来实现

**React.forwardRef**

> `Ref`转发是一项将`ref`自动地通过组件传递到其一子组件的技巧，其允许某些组件接收`ref`，并将其向下传递给子组件。

[具体实例参考链接](https://juejin.cn/post/6844903749211652104)

## 八、useImperativeHandle【避免暴露过多属性给父组件】

```js
useImperativeHandle(ref, createHandle, [deps])
```

```js
function FancyInput(props, ref) {
 const inputRef = useRef();
  
 useImperativeHandle(ref, () => ({
  focus: () => {
   inputRef.current.focus();
  }
 }));

 return <input ref={inputRef} ... />;
}

FancyInput = forwardRef(FancyInput);
```

1. 接受参数：
* 参数1: 接收一个通过`forwardRef`引用父组件的`ref`实例
* 参数2: 回调函数，返回一个对象, 对象里面存储需要暴露给父组件的属性、方法或整个组件`ref`实例

**说明：**

1. 官方建议`useImperativeHandle`应当与`forwardRef`一起使用，避免使用`ref`那样的命令式代码
2. 当我们不想向父组件暴露太多的东西的时候，可以使用`useImperativeHandle`来`按需暴露`给父组件一些东西

## 九、useLayoutEffect

> 在所有的 DOM 变更之后 同步调用 effect; 使用方式和 useEffect() 相同

**什么情况下使用：**

> 在你需要让所有的`dom`变更后同时执行所有的`useEffect`的时候来使用，可以用来读取`dom`，之后同步触发重新`render`

**说明：**

1. 在浏览器执行绘制之前，`useLayoutEffect`内部的更新计划将被同步刷新
2. 建议尽可能使用标准的`useEffect`以避免阻塞视觉更新

## 十、useDebugValue

> 在react的浏览器调试工具上显示你的自定义 hooks，或者给 hooks 标记一些东西

```js
useDebugValue(value[, callback]);
```

**接受参数：**

* 参数1：参数标记在 react 的调试工具上
* 参数2：回调函数，函数形参为 useDebugValue 的第一个参数，返回值会显示在浏览器调试工具中【回调函数中可以进行一系列操作】

各hook的具体实例理解可以参考下面👇👇👇

[参考链接](https://blog.csdn.net/weixin_43902189/article/details/99689186)