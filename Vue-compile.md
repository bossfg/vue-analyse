# vue-compile实践
## Vue.js 模板编译详解：
vue.compile
与静态内容渲染
概述
您的问题涉及 Vue.js 的模板编译系统，特别是关于为什么 <div>hello22221</div> 这样的内容在使用 vue.compile 编译后会被放入 render 函数而不是 staticRenderFns 数组中。这是一个很好的问题，因为从表面上看，这个简单的 div 确实看起来像是静态内容。

## Vue 模板编译器的工作原理
Vue.js 的模板编译器负责将 HTML 模板转换为渲染函数。编译过程主要包括三个步骤：

 - 解析(Parse)：将模板字符串解析为 AST（抽象语法树）
 - 优化(Optimize)：标记静态节点和子树
 - 生成(Generate)：生成渲染函数代码

## 静态内容与staticRenderFns
在 Vue.js 中，并不是所有看起来静态的内容都会被放入 staticRenderFns 数组。Vue 有特定的条件来判断一个节点是否应该被视为"静态根节点"。

## 源码依据
根据 Vue.js 的源码，我们可以看到编译器的工作流程：

首先，vue.compile 函数会调用内部的编译器来处理模板： README.md:19-23

编译结果包含 render 和 staticRenderFns： README.md:24-29

在代码生成阶段，generate 函数会生成 render 和 staticRenderFns： index.ts:57-71

关键在于 genElement 函数，它决定了一个元素是放入主 render 函数还是 staticRenderFns： index.ts:74-83

` 为什么<div>hello22221</div>不是静态渲染函数？根据源码，一个节点要被视为静态根节点并放入 staticRenderFns 数组，`需要满足以下条件：

 - 该节点必须被标记为静态节点（在优化阶段完成）
 - 该节点必须是一个静态根节点（有子节点的静态节点）
 - 该节点不能只有一个文本子节点（这是关键）
在 genStatic 函数中，我们可以看到静态节点是如何被处理的： index.ts:157-170

## 关键原因
Vue.js 不会将只有一个文本子节点的静态节点（如 <div>hello22221</div>）放入 staticRenderFns 数组，因为这样做的性能收益很小。将这样的简单节点提取到 staticRenderFns 中会增加函数调用的开销，而这可能超过了缓存带来的好处。

在运行时，静态渲染函数的结果会被缓存，如下所示： render-static.ts:7-25

## 实际测试
我们可以通过一个简单的测试来验证这一点：

// 简单文本节点不会被放入 staticRenderFns  
const result1 = Vue.compile('<div>hello22221</div>');  
console.log(result1.staticRenderFns.length); // 0  
  
// 复杂静态节点会被放入 staticRenderFns  
const result2 = Vue.compile('<div><p><span>hello</span></p></div>');  
console.log(result2.staticRenderFns.length); // 1
这与 Vue.js 的测试代码也是一致的： codegen.spec.ts:630-635

## 总结
<div>hello22221</div> 虽然是静态内容，但由于它只有一个简单的文本子节点，Vue 编译器认为将其提取到 staticRenderFns 中的性能收益不足以抵消额外的函数调用开销，因此它被直接放在主 render 函数中。

这是 Vue 编译器的一种优化策略，目的是在缓存静态内容和减少函数调用开销之间取得平衡。只有那些包含多个子节点或嵌套结构的复杂静态内容才会被提取到 staticRenderFns 中以获得更好的性能。

## Notes
本文主要基于 Vue.js 2.7 版本的源代码进行分析。Vue 的编译器是一个复杂的系统，本文只关注了与静态节点处理相关的部分。实际上，编译器的优化策略还包括许多其他方面，如事件处理、指令处理等。

Wiki pages you might want to explore:

Introduction to Vue.js (vuejs/vue)
Single-File Components (SFCs) (vuejs/vue)
