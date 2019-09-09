<!-- TOC -->

  - [Vuex](#vuex)
    - [vuex 原理](#vuex-原理)
    - [vuex 是什么？怎么使用？哪种功能场景使用它](#vuex-是什么怎么使用哪种功能场景使用它)
    - [vuex 有哪几种属性](#vuex-有哪几种属性)
    - [vuex 的 store 特性是什么](#vuex-的-store-特性是什么)
    - [vuex 的 getter 特性是什么](#vuex-的-getter-特性是什么)
    - [vuex 的 mutation 特性是什么](#vuex-的-mutation-特性是什么)
    - [vue 中 ajax 请求代码应该写在组件的 methods 中还是 vuex 的 action 中](#vue-中-ajax-请求代码应该写在组件的-methods-中还是-vuex-的-action-中)
    - [不用 vuex 会带来什么问题](#不用-vuex-会带来什么问题)
    - [使用 Vuex 只需执行 Vue.use(Vuex)，并在 Vue 的配置中传入一个 store 对象的示例，store 是如何实现注入的](#使用-vuex-只需执行-vueusevuex并在-vue-的配置中传入一个-store-对象的示例store-是如何实现注入的)
    - [state 内部支持模块配置和模块嵌套，如何实现的](#state-内部支持模块配置和模块嵌套如何实现的)
    - [在执行 dispatch 触发 action(commit 同理) 的时候，只需传入 (type, payload)，action 执行函数中第一个参数 store 从哪里获取的](#在执行-dispatch-触发-actioncommit-同理-的时候只需传入-type-payloadaction-执行函数中第一个参数-store-从哪里获取的)
    - [Vuex 如何区分 state 是外部直接修改，还是通过 mutation 方法修改的](#vuex-如何区分-state-是外部直接修改还是通过-mutation-方法修改的)
    - [调试时的"时空穿梭"功能是如何实现的](#调试时的时空穿梭功能是如何实现的)
- [Vuex 和 Redux 的区别](#vuex-%e5%92%8c-redux-%e7%9a%84%e5%8c%ba%e5%88%ab)

<!-- /TOC -->

## Vuex

![img](assets/68747470733a2f2f767565782e7675656a732e6f72672f767565782e706e67.png)



### vuex 原理

vuex 整体思想诞生于 flux, 可其的实现方式完完全全的使用了 vue 自身的响应式设计，依赖监听、依赖收集都属于 vue 对对象 Property set get 方法的代理劫持。最后一句话结束 vuex 工作原理，vuex 中的 store 本质就是没有 template 的隐藏着的 vue 组件；

Vuex实现了一个单向数据流，在全局拥有一个State存放数据，所有修改State的操作必须通过Mutation进行，Mutation的同时提供了订阅者模式供外部插件调用获取State数据的更新。所有异步接口需要走Action，常见于调用后端接口异步获取更新数据，而Action也是无法直接修改State的，还是需要通过Mutation来修改State的数据。最后，根据State的变化，渲染到视图上。Vuex运行依赖Vue内部数据双向绑定机制，需要new一个Vue对象来实现“响应式化”，所以Vuex是一个专门为Vue.js设计的状态管理库。



### vuex 是什么？怎么使用？哪种功能场景使用它

vue 框架中状态管理。在 main.js 引入 store，注入。新建了一个目录 store，... export 。场景有：单页应用中，组件之间的状态。音乐播放、登录状态、加入购物车

```js
// 新建 store.js
import vue from 'vue'
import vuex form 'vuex'
vue.use(vuex)
export default new vuex.store({
	//...code
})

//main.js
import store from './store'
...
```

### vuex 有哪几种属性

有 5 种，分别是 state、getter、mutation、action、module

### vuex 的 store 特性是什么

- vuex 就是一个仓库，仓库里放了很多对象。其中 state 就是数据源存放地，对应于一般 vue 对象里面的 data
- state 里面存放的数据是响应式的，vue 组件从 store 读取数据，若是 store 中的数据发生改变，依赖这相数据的组件也会发生更新
- 它通过 mapState 把全局的 state 和 getters 映射到当前组件的 computed 计算属性

### vuex 的 getter 特性是什么

- getter 可以对 state 进行计算操作，它就是 store 的计算属性
- 虽然在组件内也可以做计算属性，但是 getters 可以在多给件之间复用
- 如果一个状态只在一个组件内使用，是可以不用 getters

### vuex 的 mutation 特性是什么

- action 类似于 muation, 不同在于：action 提交的是 mutation, 而不是直接变更状态
- action 可以包含任意异步操作

### vue 中 ajax 请求代码应该写在组件的 methods 中还是 vuex 的 action 中

如果请求来的数据不是要被其他组件公用，仅仅在请求的组件内使用，就不需要放入 vuex 的 state 里

如果被其他地方复用，请将请求放入 action 里，方便复用，并包装成 promise 返回

### 不用 vuex 会带来什么问题

- 可维护性会下降，你要修改数据，你得维护 3 个地方
- 可读性下降，因为一个组件里的数据，你根本就看不出来是从哪里来的
- 增加耦合，大量的上传派发，会让耦合性大大的增加，本来 Vue 用 Component 就是为了减少耦合，现在这么用，和组件化的初衷相背



### 使用 Vuex 只需执行 Vue.use(Vuex)，并在 Vue 的配置中传入一个 store 对象的示例，store 是如何实现注入的

[美团](https://tech.meituan.com/vuex_code_analysis.html)

Vue.use(Vuex) 方法执行的是 install 方法，它实现了 Vue 实例对象的 init 方法封装和注入，使传入的 store 对象被设置到 Vue 上下文环境的 $store 中。因此在 Vue Component 任意地方都能够通过 this.$store 访问到该 store。

### state 内部支持模块配置和模块嵌套，如何实现的

[美团](https://tech.meituan.com/vuex_code_analysis.html)

在 store 构造方法中有 makeLocalContext 方法，所有 module 都会有一个 local context，根据配置时的 path 进行匹配。所以执行如 dispatch('submitOrder', payload) 这类 action 时，默认的拿到都是 module 的 local state，如果要访问最外层或者是其他 module 的 state，只能从 rootState 按照 path 路径逐步进行访问。

### 在执行 dispatch 触发 action(commit 同理) 的时候，只需传入 (type, payload)，action 执行函数中第一个参数 store 从哪里获取的

[美团](https://tech.meituan.com/vuex_code_analysis.html)

store 初始化时，所有配置的 action 和 mutation 以及 getters 均被封装过。在执行如 dispatch('submitOrder', payload) 的时候，actions 中 type 为 submitOrder 的所有处理方法都是被封装后的，其第一个参数为当前的 store 对象，所以能够获取到 { dispatch, commit, state, rootState } 等数据。

### Vuex 如何区分 state 是外部直接修改，还是通过 mutation 方法修改的

[美团](https://tech.meituan.com/vuex_code_analysis.html)

Vuex 中修改 state 的唯一渠道就是执行 commit('xx', payload) 方法，其底层通过执行 this.\_withCommit(fn) 设置、\_committing 标志变量为 true，然后才能修改 state，修改完毕还需要还原、\_committing 变量。外部修改虽然能够直接修改 state，但是并没有修改、\_committing 标志位，所以只要 watch 一下 state，state change 时判断是否、\_committing 值为 true，即可判断修改的合法性。

### 调试时的"时空穿梭"功能是如何实现的

[美团](https://tech.meituan.com/vuex_code_analysis.html)

devtoolPlugin 中提供了此功能。因为 dev 模式下所有的 state change 都会被记录下来，'时空穿梭' 功能其实就是将当前的 state 替换为记录中某个时刻的 state 状态，利用 store.replaceState(targetState) 方法将执行 this.\_vm.state = state 实现。

# Vuex 和 Redux 的区别

在 Vuex 中，$store 被直接注入到了组件实例中，因此可以比较灵活的使用：



- 使用 dispatch 和 commit 提交更新
- 通过 mapState 或者直接通过 this.$store 来读取数据

在 Redux 中，我们每一个组件都需要显示的用 connect 把需要的 props 和 dispatch 连接起来。



另外 Vuex 更加灵活一些，组件中既可以 dispatch action 也可以 commit updates，而 Redux 中只能进行 dispatch，并不能直接调用 reducer 进行修改。

从实现原理上来说，最大的区别是两点：



- Redux 使用的是不可变数据，而Vuex的数据是可变的。Redux每次都是用新的state替换旧的state，而Vuex是直接修改
- Redux 在检测数据变化的时候，是通过 diff 的方式比较差异的，而Vuex其实和Vue的原理一样，是通过 getter/setter来比较的（如果看Vuex源码会知道，其实他内部直接创建一个Vue实例用来跟踪数据变化）

