---
title: react hooks 总结
date: 2025-11-07
summary: react hooks 个人总结
category: React
tags: [react, hooks]
comments: false
---

常用的hooks api 如下：

useState、useEffect、useContext、useReducer、useCallback、useMemo、useRef

## useState

组件添加一个 **状态变量**，对状态进行管理

```js
const [state, setState] = useState(initialState)
```

- state：状态变量
- setState：更新状态的函
- initialState：初始状态

initialState 可以是一个值，也可以是一个函数，函数的返回值就是初始状态。当组件初始化时，React调用初始化函数，并返回存储为状态的初始值。

```js
function App() {
  const [count, setCount] = useState(0)
  const handlePlus = () => {
    setCount((count) => count + 1)
  }
  const handleMinus = () => {
    setCount((count) => count - 1)
  }
}
```
