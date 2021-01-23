## Vue.js 源码剖析-响应式原理、虚拟 DOM、模板编译和组件化


### 1. 请简述 Vue 首次渲染的过程。


### 2. 请简述 Vue 响应式原理。

 主要通过数据劫持、观察者模式实现
> 在 observer 方法中调用 Object.defineProperty 方法对 data 进行劫持，通过 getter 方法收集依赖，通过 setter 方法派发更新

> 每个响应式的对象都有它的 observe 对象，observe 对象中有一个事件中心 dep，dep 的 subs 数组用于储存 Wacther 对象。在 setter 的时候调用 dep.notify()，遍历 subs 数组中 Watcher 的 update方法，在 getter 的时候将 Watcher push 到对应 observe 的 dep 的 subs 数组中。


### 3. 请简述虚拟 DOM 中 Key 的作用和好处。

- 作用：Vue 中循环可以使用 v-for，可以对每一项添加 key 属性，方便跟踪节点位置。在虚拟 dom 的 diff 算法中，会比较节点的 key 等，key 作为唯一标识符，通过比较新旧虚拟节点判断节点是否可以复用


- 好处：可以提高 vnode 的复用性，减少 dom 操作，提高渲染性能

### 4. 请简述 Vue 中模板编译的过程。