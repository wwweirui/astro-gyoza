---
title: JS设计模式_工厂模式
date: 2025-11-10
lastMod: 2025-11-10T03:58:16.758Z
summary: JS设计模式之工厂模式。
category: JS
tags: [Js, JS-Base]
comments: false
---

## 工厂模式

工厂模式是指根据不同的输入返回不同类型的实例，一般用来创建同一类对象。主要思想是将对象的创建与对象的实现分离

> 对象的创建与对象的实现分离

重点是：

- 创建对象的工厂 createObj

### es5 实现

```js
function createPerson(name, age) {
  const obj = {}
  obj.name = name
  obj.age = age
  return obj
}
```

1. 创建产品类class
2. 创建工厂类class
3. 工厂类中创建产品类的实例

### es6 实现

```js
// 产品类
class User {
  constructor(name) {
    this.name = name
  }
}

// 工厂类
class UserFactory {
  static createUser(name) {
    switch (name) {
      case 'Tom':
        return new User('Tom')
      case 'Jerry':
        return new User('Jerry')
      default:
        throw new Error('Invalid user name')
    }
  }
}
```
