<!-- TOC -->

- [[vue 和 react 区别](https://juejin.im/post/5b8b56e3f265da434c1f5f76)](#vue-和-react-区别httpsjuejinimpost5b8b56e3f265da434c1f5f76)
- [vue 的双向绑定的原理是什么（常考)](#vue-的双向绑定的原理是什么常考)
- [为什么 Vue 3.0 中使用 Proxy 了](#为什么-vue-30-中使用-proxy-了)
- [Vue computed 实现原理](#vue-computed-实现原理)
- [computed 和 watch 区别](#computed-和-watch-区别)
- [组件中 data 什么时候可以使用对象](#组件中-data-什么时候可以使用对象)
- [Vue 虚拟 DOM](#vue-虚拟-dom)
- [虚拟 DOM Diff 算法](#虚拟-dom-diff-算法)
- [Vue.nextTick 的原理和用途](#vuenexttick-的原理和用途)
- [前端路由原理](#前端路由原理)
  - [Hash 模式](#hash-模式)
  - [History 模式](#history-模式)
  - [两种模式对比](#两种模式对比)
- [Vue 生命周期](#vue-生命周期)
  - [父子组件生命周期](#父子组件生命周期)
  - [兄弟组件的生命周期](#兄弟组件的生命周期)
  - [mixin](#mixin)

<!-- /TOC -->

### [vue 和 react 区别](https://juejin.im/post/5b8b56e3f265da434c1f5f76)

1. 监听数据变化的实现原理不同

   - vue 是通过 getter/setter 数据劫持的方式，进，行监听数据变化
   - React 是默认是通过比较引用的方式进行如果不优化（PureComponent/shouldComponentUpdate）可能导致大量不必要的 VDOM 的重新渲染
   - vue 强调的是可变数据，React 更强调数据不可变

2. 数据流不同

![vue-react-data](../img/vue-react-data.png)

改变数据方式不同，Vue 的底层使用了依赖追踪，支持双向绑定

React 一直提倡的是单向数据流，React 需要使用 setState 来改变状态，

Vue 的底层使用了依赖追踪，页面更新渲染已经是最优的了，但是 React 还是需要用户手动去优化这方面的问题。

3. 模板渲染方式不同

- React 是通过 JSX 渲染模板  
  React 是在组件 JS 代码中，通过原生 JS 实现模板中的常见语法，比如插值，条件，循环等，都是通过**JS 语法**实现的

- Vue 是通过一种拓展的 HTML 语法进行渲染  
  Vue 是在和组件 JS 代码分离的单独的模板中，通过**指令**来实现的，比如条件语句就需要 v-if 来实现

### vue 的双向绑定的原理是什么（常考)

vue.js 是采用数据劫持结合发布者 - 订阅者模式的方式，通过 Object.defineProperty() 来劫持各个属性的 setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

具体步骤：  
**第一步：** 需要 observe 的数据对象进行递归遍历，包括子属性对象的属性，都加上 setter 和 getter 这样的话，给这个对象的某个值赋值，就会触发 setter，那么就能监听到了数据变化

**第二步**：compile 解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图

**第三步：** Watcher 订阅者是 Observer 和 Compile 之间通信的桥梁，主要做的事情是：

- 在自身实例化时往属性订阅器 (dep) 里面添加自己
- 自身必须有一个 update() 方法
- 待属性变动 dep.notice() 通知时，能调用自身的 update() 方法，并触发 Compile 中绑定的回调，则功成身退。

**第四步：** MVVM 作为数据绑定的入口，整合 Observer、Compile 和 Watcher 三者，通过 Observer 来监听自己的 model 数据变化，通过 Compile 来解析编译模板指令，最终利用 Watcher 搭起 Observer 和 Compile 之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化 (input) -> 数据 model 变更的双向绑定效果。

vue 首先会调用所有使用的数据，从而触发所有的 getter 函数，进而通过 Dep 对象收集所有响应式依赖，然后调用所有 watcher 执行 render 操作，其中会进行虚拟 DOM 的存储和比较，进而渲染页面

当有数据变更时会触发 setter 函数，触发 dep.notify()，进而调用 Watcher 的 update，推入 Vue 的异步观察队列中，最终调用 Watch 的 getter 或者 cb 方法进而调用 vm.\_update(), 再调用 vm**patch**方法进行虚拟 DOM 的 diff，并最终渲染到页面。

![vue-data](../img/vue-data.png)

### 为什么 Vue 3.0 中使用 Proxy 了

> Proxy 是 es6 的新特性，意为代理，可以在目标对象前架设一层拦截，外界对该对象的访问，都必须通过这层拦截，这提供了一种机制，可以对外界的访问进行过滤和改写。

1. Vue 中使用 Object.defineProperty 进行双向数据绑定时，告知使用者是可以监听数组的，但是只是监听了数组的 push()、pop()、shift()、unshift()、splice()、sort()、reverse() 这八种方法，其他数组的属性检测不到。

2. Object.defineProperty 只能劫持对象的属性，因此对每个对象的属性进行遍历时，如果属性值也是对象需要深度遍历，那么就比较麻烦了，所以在比较 Proxy 能完整劫持对象的对比下，选择 Proxy。

3. 为什么 Proxy 在 Vue 2.0 编写的时候出来了，尤大却没有用上去？因为当时 es6 环境不够成熟，兼容性不好，尤其是这个属性无法用 polyfill 来兼容。（polyfill 是一个 js 库，专门用来处理 js 的兼容性问题 -js 修补器）

1、Proxy 可以劫持整个对象，避免了 Object.defineProperty 的遍历和递归，提高下性能和效率 2、有 13 种劫持操作

![vue-data](../img/vue-data- 原理。png)

### Vue computed 实现原理

计算属性的主要应用场景是代替模板内的表达式，或者 data 值的任何复杂逻辑都应该使用 computed 来计算，它有两大优势：

1、逻辑清晰，方便于管理

2、计算值会被缓存，依赖的 data 值改变时才会从新计算

把计算属性的 key 挂载到 vm 对象下，并使用 Object.defineProperty 进行处理

解析下图：
调用 vue.getfullname 第一次会打印 '----- 走了 computed 之 getfullname------'，即计算属性第一次计算了值，第二次调用时，不会再打印值即直接获取的缓存值，为什么第二次是获得的缓存值呢，因为第二次执行时 watcher.dirty=true，就会直接返回 watcher.value 值。
![ss](../img/vue-computed.png)

解析下图：
运行 vue.getfullname 时会执行 computedGetter 函数，因为 watcher.dirty=true 因此会从新计算值，因此会打印 '----- 走了 computed 之 getfullname------'，值为'HI world', 再次执行只会获得计算属性的缓存值。
![ss](../img/vue-computed2.png)

### computed 和 watch 区别

computed 是计算属性，依赖其他属性计算值，并且 computed 的值有缓存，只有当计算值变化才会返回内容。  
computed 对应的是一个值依赖多个值

watch 监听到值的变化就会执行回调，在回调中可以进行一些逻辑操作。
watch 是一个值会影响对个值

### 组件中 data 什么时候可以使用对象

组件复用时所有组件实例都会共享 data，如果 data 是对象的话，就会造成一个组件修改 data 以后会影响到其他所有组件，所以需要将 data 写成函数，每次用到就调用一次函数获得新的数据。

当我们使用 new Vue() 的方式的时候，无论我们将 data 设置为对象还是函数都是可以的，因为 new Vue() 的方式是生成一个根组件，该组件不会复用，也就不存在共享 data 的情况了。

### Vue 虚拟 DOM

真正的 DOM 元素是非常庞大的，因为浏览器的标准就把 DOM 设计的非常复杂。当我们频繁的去做 DOM 更新，会产生一定的性能问题。

1. 但是对 JS 的操作是高效，可以在某些程度上提高性能，因为频繁的修改 DOM 操作是很耗性能的工作，通过虚拟 DOM 可以将修改操作最小化，提高性能
2. 更重要的是虚拟 DOM 对组件进行了一层抽象，使得开发的应用可以移植到不同的平台
3. vdom 作为一个兼容层可以实现跨端开发，服务端渲染，以及提供一个性能还算不错 Dom 更新策略。
4. 提供了一种方便的工具，使得开发效率得到保证

### 虚拟 DOM Diff 算法

vue 采用的同层比较的方式，对比树的每个层的差异，记录节点之间的差异，然后最小化更新。  
节点间的差异可以归纳为 4 种情况：

1. 修改了节点的属性
2. 修改了节点的文本内容
3. 节点类型变了，替换的原有的节点
4. 节点的顺序，数量发生的更改，节点的移动、增加、删除，这会通过计算最小编辑距离来最小化的修改 DOM，Vue 会尽可能复用 DOM，尽可能不发生 DOM 的移动。

记录完新旧树的差别之后就深度遍历 DOM，将 DIFF 的内容更新

patch 的策略是：

1. 如果 vnode 不存在但是 oldVnode 存在，说明意图是要销毁老节点，那么就调用 invokeDestroyHook(oldVnode) 来进行销毁

2. 如果 oldVnode 不存在但是 vnode 存在，说明意图是要创建新节点，那么就调用 createElm 来创建新节点

3. 当 vnode 和 oldVnode 都存在时

- 如果 oldVnode 和 vnode 是同一个节点，就调用 patchVnode 来进行 patch

- 当 vnode 和 oldVnode 不是同一个节点时，如果 oldVnode 是真实 dom 节点或 hydrating 设置为 true，需要用 hydrate 函数将虚拟 dom 和真是 dom 进行映射，然后将 oldVnode 设置为对应的虚拟 dom，找到 oldVnode.elm 的父节点，根据 vnode 创建一个真实 dom 节点并插入到该父节点中 oldVnode.elm 的位置

- 这里面值得一提的是 patchVnode 函数，因为真正的 patch 算法是由它来实现的（patchVnode 中更新子节点的算法其实是在 updateChildren 函数中实现的，为了便于理解，我统一放到 patchVnode 中来解释）。

### Vue.nextTick 的原理和用途

在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

1. DOM 更新循环是指什么？
   Vue 是异步执行 DOM 更新的，同步任务执行完毕，开始执行异步 watcher 队列的任务，更新 DOM 。Vue 在内部尝试对异步队列使用原生的 Promise.then 和 MessageChannel 方法，如果执行环境不支持，会采用 setTimeout(fn, 0) 代替。
2. 下次更新循环是什么时候？
3. 修改数据之后使用，是加快了数据更新进度吗？
4. 在什么情况下要用到？
   需要在视图更新之后，基于新的视图进行操作。
   created 和 mounted 阶段，如果需要操作渲染后的试图，也要使用 nextTick 方法。

- 获取隐藏元素的 dom 属性

### 前端路由原理

前端路由实现起来其实很简单，本质就是监听 URL 的变化，然后匹配路由规则，显示相应的页面，并且无须刷新页面。目前前端使用的路由就只有两种实现方式。

- Hash 模式
- History 模式

#### Hash 模式

`www.test.com/#/` 就是 Hash URL，当 # 后面的哈希值发生变化时，可以通过 hashchange 事件来监听到 URL 的变化，从而进行跳转页面，并且无论哈希值如何变化，服务端接收到的 URL 请求永远是 www.test.com。

```js
window.addEventListener('hashchange', () => {
  // ... 具体逻辑
})
```

#### History 模式

History 模式是 HTML5 新推出的功能，主要使用 `history.pushState` 和 `history.replaceState` 改变 URL。

通过 History 模式改变 URL 同样不会引起页面的刷新，只会更新浏览器的历史记录。

```js
// 新增历史记录
history.pushState(stateObject, title, URL)
// 替换当前历史记录
history.replaceState(stateObject, title, URL)
```

当用户做出浏览器动作时，比如点击后退按钮时会触发 popState 事件

```js
window.addEventListener('popstate', e => {
  // e.state 就是 pushState(stateObject) 中的 stateObject
  console.log(e.state)
})
```

#### 两种模式对比

- Hash 模式只可以更改 # 后面的内容，History 模式可以通过 API 设置任意的同源 URL
- History 模式可以通过 API 添加任意类型的数据到历史记录中，Hash 模式只能更改哈希值，也就是字符串
- Hash 模式无需后端配置，并且兼容性好。History 模式在用户手动输入地址或者刷新页面的时候会发起 URL 请求，后端需要配置 index.html 页面用于匹配不到静态资源的时候

### Vue 生命周期

![vue-lifecircle](../img/vue-lifecircle.png)

![vue-lifecircle](../img/vue-lifecircle-desc.png)

1、created 阶段的 ajax 请求与 mounted 请求的区别：前者页面视图未出现，如果请求信息过多，页面会长时间处于白屏状态  
2、mounted 不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染
完毕，可以用 vm.\$nextTick

#### 父子组件生命周期

1、仅当子组件完成挂载后，父组件才会挂载  
2、当子组件完成挂载后，父组件会主动执行一次 beforeUpdate/updated 钩子函数（仅首次）  
3、父子组件在 data 变化中是分别监控的，但是在更新 props 中的数据是关联的（可实践）
4、销毁父组件时，先将子组件销毁后才会销毁父组件

#### 兄弟组件的生命周期

1、组件的初始化（mounted 之前）分开进行，挂载是从上到下依次进行  
2、当没有数据关联时，兄弟组件之间的更新和销毁是互不关联的

#### mixin

1、mixin 中的生命周期与引入该组件的生命周期是仅仅关联的，且 mixin 的生命周期优先执行 ，顺序执行
2、自身 data 优先级高于混入的 data 属性
