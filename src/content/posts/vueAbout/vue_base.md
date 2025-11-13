---
title: Vue系列_响应式
date: 2025-11-10
lastMod: 2025-11-10T15:58:16.758Z
summary: Vue3 响应式
category: Vue
tags: [Vue]
comments: false
---

## 设计逻辑

“分层解耦、渐进式增强”

围绕“数据响应式”和“虚拟DOM”，两大核心，拆分为多个指责明确的模块

> 基础层

提供底层工具、常量、类型定义：工具函数（utils）、常量（constants）

职责：提供全源码复用的工具函数、常量定义、类型声明，是所有模块的基础。

核心逻辑：抽象通用能力，避免重复代码，保证源码一致性。

> 核心层 响应式系统（Reactivity）
>
> > 职责：实现 “数据变化→自动更新视图”，是 Vue 的灵魂模块，不依赖任何其他核心模块（可独立使用）

数据响应式、虚拟DOM、组件基础能力：响应式系统（reactivity）、VNode、组件系统

关键文件（Vue3）：packages/reactivity/
核心 API：reactive.ts（代理对象）、ref.ts（基础类型响应式）、computed.ts（计算属性）、effect.ts（副作用函数）。
辅助逻辑：track.ts（依赖收集）、trigger.ts（触发更新）、baseHandlers.ts（Proxy 拦截器）。

- 代理拦截
- 依赖收集
- 触发更新

虚拟 DOM（Virtual DOM）

职责：用 JavaScript 对象描述 DOM 结构（VNode），提供 “虚拟 DOM→真实 DOM” 的转换逻辑，是跨平台渲染的基础。

- 关键文件（Vue3）：packages/runtime-core/
  - VNode 创建：vnode.ts（createVNode 函数，生成 VNode 对象，包含 type、props、children 等属性）。
  - 虚拟 DOM 操作：patch.ts（核心！对比新旧 VNode，计算最小更新差异，映射到真实 DOM 操作）。
  - 辅助逻辑：shapeFlags.ts（VNode 类型标记，优化 patch 逻辑）、h.ts（h 函数，简化 VNode 创建）。

> 应用层

应用初始化、全局API、生命周期管理：应用实例（App）、全局API、生命周期钩子

> 编译层

模版 -> 渲染函数（编译时/运行时）：模板解析（parse）、转换（transform）、生成（generate）

## proxy 高级应用场景

### 响应式数据（Vue3 核心原理）

- 监听整个对象（无需遍历属性）
- 监听数组索引、长度、方法调用
- 监听新增属性（proxy.newKey = value）

简化版Vue3响应式实现：

```js
// 依赖收集
const depMap = new Map() // key: target+prop, value: Set<更新函数>

// 追踪依赖
function track(target, prop) {
    conost key = `${target}-${prop}`;
    if(!depMap.has(key)) {
        depMap.set(key, new Set())
    }
    // 假设更新函数是 update 实际vue中是组件渲染函数
    depMap.get(key).add(update)
}

// 触发更新（属性修改时执行）
function trigger(target, prop) {
    const key = `${target}-${prop}`;
    depMap.get(key)?.forEach(fn => fn())
}

// 响应式工厂
function reactive(target) {
    return new Proxy(target, {
        get(target, prop, receiver) {
            // 读取数据-收集依赖
            track(target, prop)
            return Reflect.get(target, prop, receiver)
        },
        set(target, prop, value, receiver) {
            const oldValue = Reflect.get(target, prop, receiver)
            if(oldValue !== value) {
                // 修改数据-触发更新
                Reflect.set(target, prop, value, receiver)
                trigger(target, prop)
            }
            return true
        }
    })
}


function updateFn() {
  console.log("数据更新：");
}

```

## 问题

1. 嵌套对象需要递归代理
   Proxy 只代理目标对象的 “第一层属性”，若属性是对象（嵌套对象），需递归创建代理才能监听深层变化：

2. 兼容性问题
   需要做兼容降级处理：Proxy 是 ES6 特性，不支持 IE 浏览器，若需兼容老项目，需使用 Object.defineProperty 或 polyfill（但 polyfill 无法完全模拟所有拦截器）。

3. this 指向问题
   代理对象的 this 指向 proxy 本身，而非 target。若 target 是有状态的对象（如类实例），可能导致意外行为：

4. 为什么 proxy要搭配reflect 呢？

reflect 提供了与proxy拦截器一一对应的“原生操作接口”。解决了proxy拦截后，“**如何正确执行原对象行为**”的关键问题

简单来说： proxy 负责“拦截操作”，reflect 负责“执行原对象操作”。

**保持原生行为的一致性，避免手动模拟出错**

Proxy 拦截的是对象的底层操作（如属性访问、赋值），若想在拦截后 “执行原对象的原生逻辑”，直接操作 target（如 target[prop]）可能存在兼容性或逻辑漏洞，而 Reflect 的方法与 Proxy 拦截器完全对齐，是官方推荐的 “原生操作调用方式”。

**解决 this 指向问题（关键场景：继承 / 代理上下文）**

ES6 设计 Reflect 的核心目的之一，就是为 Proxy 提供 “原生操作的标准接口”。在未来的 JavaScript 版本中，可能会新增更多 Proxy 拦截器，而 Reflect 会同步更新，确保代码的前瞻性。

> 不搭配reflect 会出现什么问题呢？

- this 指向错误：继承场景、代理上下文下的this不指向预期对象
- 原生行为丢失：拦截器不完整，无法执行原对象的原生逻辑
- 兼容性问题：某些操作无法兼容，如 Reflect.defineProperty
- 错误处理困难： 无法统一错误处理，如 Reflect.defineProperty 可能抛出 TypeError

## 总结

Proxy 是 JavaScript 中强大的 “元编程工具”，核心价值是无侵入式拦截对象操作，支持更广泛的场景（响应式、校验、缓存、监控等）。相比 Object.defineProperty，它更灵活、功能更全面，是现代前端框架和工具库的首选方案。
掌握 Proxy 的关键：

1. 理解 “代理” 本质：不修改原对象，通过中间层拦截操作；
2. 熟练常用拦截器：get/set（属性操作）、apply/construct（函数 / 构造函数）；
3. 结合 Reflect 使用：保持原生行为，避免直接操作 target；
4. 注意嵌套代理和 this 指向问题。

无论是开发框架、状态管理库，还是日常业务中的数据校验、日志监控，Proxy 都能大幅提升代码的灵活性和可维护性。
