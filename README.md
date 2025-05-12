# Vue.js 架构详解
## 概述
Vue.js 是一个渐进式的 JavaScript 框架，用于构建用户界面。它的核心是一个响应式的、基于组件的视图层，易于集成到其他项目中。Vue.js 的架构设计使其既可以作为库使用，也可以作为完整的框架使用。

## 核心架构
Vue.js 的架构主要由以下几个部分组成：

![image](https://github.com/user-attachments/assets/943859d8-c11f-49bd-a1d8-77e57c92db19)

## 1. 核心运行时
Vue.js 的核心运行时是整个框架的基础，它负责初始化 Vue 实例、处理生命周期、管理组件等。

Vue 实例的创建过程如下：
![image](https://github.com/user-attachments/assets/eefe2c40-7579-4a8e-bdd7-723f29e7d310)

index.ts:9-13

Vue 构造函数非常简洁，它主要调用 _init 方法来初始化实例。这个方法在 initMixin 中定义： init.ts:16-81

## 2. 组件系统
Vue.js 的组件系统是其最强大的特性之一，它允许开发者创建可重用的自包含单元。

组件的本质是一个拥有预定义选项的 Vue 实例。组件可以嵌套，形成组件树：
![image](https://github.com/user-attachments/assets/c25f1c3f-81ad-4b8d-81b7-532c599f084d)
组件的创建过程： create-component.ts:101-127

当创建组件时，Vue 首先会将组件选项转换为构造函数，然后创建组件实例。

## 3. 响应式系统
Vue.js 的响应式系统是其最核心的特性，它使得数据变化能够自动反映到视图上。

在 Vue 2.x 中，响应式系统基于 Object.defineProperty 实现，而在 Vue 3 中则使用了 ES6 的 Proxy。

响应式系统的工作流程：
![image](https://github.com/user-attachments/assets/c3b5a275-2a78-4600-882f-cb859bd8cb2e)

## 4. 虚拟 DOM
Vue.js 使用虚拟 DOM 来提高渲染性能。虚拟 DOM 是真实 DOM 的 JavaScript 表示，它允许 Vue 在内存中进行 DOM 操作，然后只将必要的更改应用到实际 DOM 上。

虚拟 DOM 的工作流程：
![image](https://github.com/user-attachments/assets/18a92f89-e85d-4296-9474-ddd1135c91eb)

虚拟 DOM 的创建和更新过程： lifecycle.ts:62-144

_update 方法负责将虚拟 DOM 应用到真实 DOM 上，它是组件更新的核心。

## 5. 模板编译
Vue.js 允许开发者使用模板语法来声明式地描述 UI。模板会被编译成渲染函数，最终生成虚拟 DOM。

模板编译过程：
![image](https://github.com/user-attachments/assets/4c401d17-87bd-49f9-8a8e-7e33616913f3)

模板编译器的主要功能： README.md:19-30

## Vue 2.7 与 Vue 3 的区别
Vue 2.7 是 Vue 2.x 的最后一个次要版本，它向后移植了 Vue 3 的许多功能，同时保持与 Vue 2.x 应用程序的向后兼容性。

主要区别：
![image](https://github.com/user-attachments/assets/ca0bd81f-f9f3-4dc4-9c85-638b9ed3735e)

## 主要差异：

| 特性         | Vue 2.7                                      | Vue 3                                           |
|:-------------|:---------------------------------------------|:------------------------------------------------|
| 响应式系统   | 使用 `Object.defineProperty`，后移植了 `reactive()`、`ref()` | 使用 ES6 Proxies（更强大，更少限制）           |
| 组件 API     | 选项式 API 为主，组合式 API 后移植           | 组合式 API 原生支持，选项式 API 仍然支持        |
| 渲染性能     | 标准虚拟 DOM                                 | 优化的虚拟 DOM，编译器辅助优化                 |
| Tree-shaking | 有限                                         | 完全支持                                        |
| TypeScript 支持 | 后移植的改进类型                          | 从头开始使用 TypeScript 构建                   |
| Fragment 支持 | 后移植                                       | 原生                                            |
| 多根节点     | 需要包装元素                                 | 支持多根节点                                    |
| 包结构       | 单体                                         | 模块化，有单独的包                              |

## 组件生命周期
Vue 组件有一系列的生命周期钩子，允许你在组件的不同阶段执行代码：

![image](https://github.com/user-attachments/assets/f7c9a92d-b087-4eab-88a5-c763ae0578cc)

生命周期钩子的实现： lifecycle.ts:177-243

mountComponent 方法负责组件的挂载过程，它会创建渲染 watcher 并调用相应的生命周期钩子。

## 渲染过程
Vue 组件的渲染过程包括初始渲染和更新渲染：
![image](https://github.com/user-attachments/assets/1cba4076-847a-4605-84f3-3ebf32c2f5c5)
渲染函数的初始化： render.ts:21-44

组件通信方式
## Vue 组件之间有多种通信方式：

 - Props（父到子）：父组件通过 props 向子组件传递数据
 - 事件（子到父）：子组件通过事件向父组件发送消息
 - 插槽（父到子）：父组件通过插槽向子组件传递内容
 - Provide/Inject（跨层级）：祖先组件向所有后代组件提供数据
 - Vuex（全局状态管理）：管理应用的全局状态
 - 
## 总结
Vue.js 的架构设计使其成为一个灵活、高效的前端框架。它的核心特性包括：

 - 响应式数据绑定：数据变化自动反映到视图
 - 组件化开发：构建可重用的 UI 组件
 - 虚拟 DOM：高效的 DOM 更新
 - 模板编译：直观的模板语法
 - 渐进式设计：可以逐步采用
 - Vue 2.7 作为 Vue 2.x 的最后一个版本，提供了向 Vue 3 过渡的桥梁，同时保持了与现有 Vue 2.x 应用的兼容性。

## Notes
本文主要基于 Vue.js 2.7 版本的源代码进行分析，重点介绍了 Vue 的核心架构、组件系统、响应式原理、虚拟 DOM 和生命周期等关键概念。Vue 3 相关内容仅作为对比提及，未深入分析其实现细节。

Wiki pages you might want to explore:

Vue.js 2.7 vs Vue 3 (vuejs/vue)
Component System (vuejs/vue)
