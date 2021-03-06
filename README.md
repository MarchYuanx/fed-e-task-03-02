## Vue.js 源码剖析-响应式原理、虚拟 DOM、模板编译和组件化


### 1. 请简述 Vue 首次渲染的过程。
1. 初始化静态成员
    - 初始化Vue.config 对象，设置 keep-alive 组件，注册 Vue.use()、Vue.mixin()、Vue.extend() 等方法
2. 初始化实例成员
    - 调用_init()，初始化生命周期，初始化事件，调用 beforeCreate() 
    - 初始化 _props/methods/_data/computed/watch，对数据进行响应式处理并注入 vue 实例中
3. 调用 vm.$mount()，把模板编译成 render() 函数
4. 调用 mountComponent()，触发 beforeMount()，定义 updateComponent()
    - updateComponent() 中会调用 vm._update(vm._render())，_render() 用于渲染虚拟dom，_update() 用于把虚拟 dom 转换成真实 dom，并挂载在 dom 树上
5. 创建 Watcher 对象，调用 Watcher.get()，其中会调用 updateComponent()

6. 调用 mounted()，挂载结束，最终返回Vue实例。

### 2. 请简述 Vue 响应式原理。

 主要通过数据劫持、观察者模式实现
> 在 observer 方法中调用 Object.defineProperty 方法对 data 进行劫持，通过 getter 方法收集依赖，通过 setter 方法派发更新

> 每个响应式的对象都有它的 observe 对象，observe 对象中有一个事件中心 dep，dep 的 subs 数组用于储存 Wacther 对象。在 setter 的时候调用 dep.notify()，遍历 subs 数组中 Watcher 的 update方法，在 getter 的时候将 Watcher push 到对应 observe 的 dep 的 subs 数组中。


### 3. 请简述虚拟 DOM 中 Key 的作用和好处。

- 作用：Vue 中循环可以使用 v-for，可以对每一项添加 key 属性，方便跟踪节点位置。在虚拟 dom 的 diff 算法中，会比较节点的 key 等，key 作为唯一标识符，通过比较新旧虚拟节点判断节点是否可以复用


- 好处：可以提高 vnode 的复用性，减少 dom 操作，提高渲染性能

### 4. 请简述 Vue 中模板编译的过程。
在 $mount() 中的 compileToFunctions() 中处理
1. compileToFunctions(template, ...)
    - 首先从缓存中加载编译好的 render 函数
    - 若缓存中没有，调用 compile(template, options) 开始编译

2. compile(template, options)

    - 合并 options
    - 调用 baseCompile(template.trim(), finalOptions)

3. baseCompile(template.trim(), finalOptions)

    - parse()：把 template 转换成 AST tree

    - optimize()

        - 标记 AST tree 中的静态 sub trees
        - 检测到静态子树，设置为静态，不需要在每次重新渲染的时候重新生成节点
        - patch 阶段跳过静态子树
    - generate()：AST tree 生成 js 的创建代码
4. compileToFunctions(template, ...)

    - 继续把上一步中生成的字符串形式的 js 代码转换为函数
    - createFunction()
    - render 和 staticRenderFns 初始化完毕，挂载到 Vue 实例的 options 对应的属性中