---
title: JS设计模式_策略模式
date: 2025-11-10
lastMod: 2025-11-10T03:58:16.758Z
summary: JS设计模式之策略模式。
category: JS
tags: [Js, JS-Base]
comments: false
---

## 策略模式

策略模式是指定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。策略模式可以让算法的变化独立于使用算法的客户。

## 主要概念

1. Context 上下文，根据不同的策略来执行不同的算法
2. Strategy 策略，定义了算法的接口，含有具体的算法
3. StrategyMap 具体策略，实现了算法的接口

```js
// 策略模式
const StrategyMap = {}

function context(type, ...rest) {
  return StrategyMap[type] && StrategyMap[type](...rest)
}

StrategyMap.add = (a, b) => {
  return a + b
}

// 使用策略模式
context('add', 1, 2) // 3
```

## 模式优点

- 算法可以自由切换
- 避免使用多重条件判断
- 扩展性良好

## 模式缺点

- 策略类会增多
- 所有策略类都需要对外暴露

## 模式使用场景

- 多个类只有在算法或行为上稍有不同的场景
- 算法需要自由切换的场景
- 需要屏蔽算法规则的场景
