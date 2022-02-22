# VirtualDOM (虚拟DOM)

## 什么是虚拟DOM
是由普通的JS对象来描述DOM对象

- 真实DOM成员
```js
let element = document.querySelector('#app')
let s = ''
for (let key in element) {
    s += key + ','
}
console.log(s) // 非常多～
```

- 使用 Virtual DOM 来描述真实 DOM
```js
{
    sel: 'div',
    data: {},
    children: undefined,
    text: 'Hello Virtual DOM',
    elm: undefined,
    key: undefined
}
```

## 为什么要使用 Virtual DOM
- 前端开发刀耕火种的时代
- MVVM框架解决视图和状态同步问题
- 模版引擎可以简化视图操作，没办法跟踪状态
- 虚拟DOM跟踪状态变化
- 参考github上[virtual-dom](https://github.com/Matt-Esch/virtual-dom)的动机描述
    - 虚拟DOM可以维护程序的状态，跟踪上一次的状态
    - 通过比较前后两次状态差异更新真实DOM

## 虚拟DOM的作用和虚拟DOM库

### 虚拟DOM的作用
- 维护视图和状态的关系
- 复杂视图情况下提升渲染性能
- 跨平台
    - 浏览器平台渲染DOM
    - 服务端渲染SSR（Nuxt.js/Next.js）
    - 原生应用（Weex/React Native）
    - 小程序（mpvue/uni-app）等

### 虚拟DOM库
- Snabbdom
    - Vue.js2.x内部使用的虚拟DOM就是改造的 Snabbdom
    - 大约 200 SLOC(single line of code)
    - 通过模块可扩展
    - 源码使用 TypeScript 开发
    - 最快的 Virtual DOM 之一

- virtual-dom

## Snabbdom基本使用

- [案例demo](https://github.com/Humphrey2021/Snabbdom-demo)

### Snabbdom 文档
#### 看文档的意义
    - 学习任何一个库都要先看文档
    - 通过文档了解库的作用
    - 看文档中提供的示例，自己快速实现一个demo
    - 通过文档查看API的使用
- Snabbdom 文档
    - [https://github.com/snabbdom/snabbdom](https://github.com/snabbdom/snabbdom)

### Snabbdom 模块
- Snabbdom的核心库并不能处理DOM元素的属性/样式/事件等，可以通过注册Snabbdom默认提供的模块来实现
- Snabbdom中的模块可以用来拓展Snabbdom的功能
- Snabbdom中的模块的实现是通过注册全局的钩子函数来实现的

官方提供的模块
attributes/props/dataset/class/style/eventlisteners

模块的使用步骤
- 导入需要的模块
- init() 中注册模块
- h() 函数的第二个参数处使用模块

### [Snabbdom 源码解析](https://github.com/Humphrey2021/Snabbdom)

#### 如何学习源码
- 宏观了解
- 带着目标看源码
- 看源码的过程要不求甚解
- 调试
- 参考资料

#### Snabbdom和核心
- init() 设置模块，创建patch()函数
- 使用h()函数创建JS对象(VNode)描述真实DOM
- patch()比较新旧两个VNode
- 把变化的内容更新到真实DOM树

#### Snabbdom源码
- 官方源码地址
    - [https://github.com/snabbdom/snabbdom](https://github.com/snabbdom/snabbdom)
    - 版本：v2.1.0
- 克隆代码
    - git clone -b v2.1.0 --depth=1 https://github.com/snabbdom/snabbdom.git

#### h函数介绍
- 作用：创建VNode对象
- Vue中的h函数
    ```js
    new Vue({
        render: h => h(App)
    }).$mount('#app')
    ```
- h函数最早见于`hyperscript`，使用JS创建超文本

##### 函数的重载
- 参数个数或参数类型不同的函数
- js中没有重载的概念
- TS 中有重载，不过重载的实现还是通过代码调整参数
###### 函数的重载-参数个数
```ts
function add (a: number, b: number) {
    console.log(a + b)
}
function add (a: number, b: number, c: number) {
    console.log(a + b + c)
}
add(1, 2) // 3
add(1, 2, 3) // 6
```
###### 函数的重载-参数类型
```ts
function add (a: number, b: number) {
    console.log(a + b)
}
function add (a: number, b: string) {
    console.log(a + b)
}
add(1, 2) // 3
add(1, '2') // 12
```

#### patch整体过程分析
- patch(oldVnode, newVnode)
- 把新节点中变化的内容渲染到真实DOM，最后返回新节点作为下一次处理的旧节点
- 对比新旧VNode是否相同节点（节点的key和sel相同）
- 如果不是相同节点，删除之前的内容，重新渲染
- 如果是相同节点，再判断新的VNode是否有text，如果有并且和oldVnode的text不同，直接更新文本内容
- 如果新的VNode有children，判断子节点是否有变化
