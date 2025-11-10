---
title: JS设计模式_抽象工厂模式
date: 2025-11-10
lastMod: 2025-11-10T03:58:16.758Z
summary: JS设计模式之抽象工厂模式。
category: JS
tags: [Js, JS-Base]
comments: false
---

## 抽象工厂模式

抽象工厂模式提供一个创建一系列相关或相互依赖对象的家族，而无须指定它们具体的类。 抽象工厂模式主要包含4种角色：抽象工厂、具体工厂、抽象产品、具体产品。 抽象类提供接口的定义，具体类需要实现抽象接口。

抽象产品-具体产品：

- 抽象产品：定义了产品的接口，规定了产品的方法。
- 具体产品：实现了产品的接口，提供了具体的产品。
  抽象工厂-具体工厂：
- 抽象工厂：定义了工厂的接口，规定了工厂的方法。
- 具体工厂：实现了工厂的接口，提供了具体的工厂。

```ts
// 抽象产品
interface Button {
  paint(): void
}

interface Label {
  paint(): void
}
// 具体产品
class WinButton implements Button {
  paint() {
    console.log('WinButton is painted')
  }
}

class WinLabel implements Label {
  paint() {
    console.log('WinLabel is painted')
  }
}

// 抽象工厂
interface AbstractFactory {
  createButton(): Button
  createLabel(): Label
}

// 具体工厂
class WinFactory implements AbstractFactory {
  createButton() {
    return new WinButton()
  }
  createLabel() {
    return new WinLabel()
  }
}

// 抽象封装的客户端：
class App {
  // 抽象工厂统一产出
  private factory: AbstractFactory
  // 客户端可以通过构造函数传入具体工厂
  constructor(factory: AbstractFactory) {
    this.factory = factory
  }

  createUI() {
    const button: Button = this.factory.createButton()
    const label: Label = this.factory.createLabel()
    button.paint()
    label.paint()
  }
}

const app = new App(new WinFactory())
```
