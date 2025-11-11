---
title: JS设计模式_代理模式
date: 2025-11-10
lastMod: 2025-11-10T05:58:16.758Z
summary: 代理模式。
category: JS
tags: [Js, JS-Base]
comments: false
---

## 代理模式

代理（委托）模式，它为为目标创建一个代理对象，控制对目标的访问

- ES5 使用 Object.defineProperty 实现
- ES6 使用 Proxy 实现

```js
// ES5 实现
let target = {
  name: 'Tom',
  age: 18,
}
let proxy = {}

Object.keys(target).forEach((key) => {
  Object.defineProperty(proxy, key, {
    get: function () {
      return target[key]
    },
    set: function (newVal) {
      target[key] = newVal
    },
  })
})
```
