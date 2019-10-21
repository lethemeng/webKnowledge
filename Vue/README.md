<!-- TOC -->

- [v-bind 和 v-model 的区别](#v-bind-和-v-model-的区别)
- [v-show 与 v-if 有什么区别？](#v-show-与-v-if-有什么区别)
- [组件之间的传值](#组件之间的传值)
- [vue2.0 数组赋值监听](#vue20-数组赋值监听)
- [在哪个生命周期内调用异步请求？](#在哪个生命周期内调用异步请求)
- [父组件可以监听到子组件的生命周期吗？](#父组件可以监听到子组件的生命周期吗)
- [自定义指令 (v-check, v-focus) 的方法有哪些？它有哪些钩子函数？还有哪些钩子函数参数](#自定义指令-v-check-v-focus-的方法有哪些它有哪些钩子函数还有哪些钩子函数参数)
- [说出至少 4 种 vue 当中的指令和它的用法](#说出至少-4-种-vue-当中的指令和它的用法)
- [谈谈你对 keep-alive 的了解？](#谈谈你对-keep-alive-的了解)
- [组件中 data 为什么是一个函数？](#组件中-data-为什么是一个函数)

<!-- /TOC -->

### v-bind 和 v-model 的区别

1.v-bind 用来绑定数据和属性以及表达式，缩写为'：'
2.v-model 使用在表单中，实现双向数据绑定的，在表单元素外使用不起作用



```js
<input v-model='something'>
    
相当于

<input v-bind:value="something" v-on:input="something = $event.target.value">	
```

自定义组件中，v-model 默认会利用名为 value 的 prop 和名为 input 的事件，

```
父组件：
<ModelChild v-model="message"></ModelChild>

子组件：
<div>{{value}}</div>

props:{
    value: String
},
methods: {
  test1(){
     this.$emit('input', '小红')
  },
},
```



### v-show 与 v-if 有什么区别？

**v-if** 是**真正**的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建；也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

**v-show** 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 的 “display” 属性进行切换。

所以，v-if 适用于在运行时很少改变条件，不需要频繁切换条件的场景；v-show 则适用于需要非常频繁切换条件的场景。

### 组件之间的传值

- `props/$emit` 父子组件
  - 父组件与子组件传值  props
  - 子组件向父组件传递数据 $emit
- `ref` 与 `$parent / $children` 父子组件

```js

//children，parent 并不保证顺序，也不是响应式的 只能拿到一级父组件和子组件
//父组件
mounted(){
  console.log(this.$children) 
  //可以拿到 一级子组件的属性和方法
  //所以就可以直接改变 data,或者调用 methods 方法
}

//子组件
mounted(){
  console.log(this.$parent) //可以拿到 parent 的属性和方法
}
```

- EventBus `$emit $on`   父子组件 兄弟组件

-  `Vuex`   父子组件 兄弟组件

- `$attr $listeners`   **隔代组件通信**

  ``` vue
  //$attrs attrs获取子传父中未在 props 定义的值
  // 父组件 
  <home title="这是标题" width="80" height="80" imgUrl="imgUrl"/>
  
  // 子组件
  mounted() {
    console.log(this.$attrs) //{title: "这是标题", width: "80", height: "80", imgUrl: "imgUrl"}
  }
  // $listeners 子组件调用父组件的方法
  // 父组件
  <home @change="change"/>
  
  // 子组件
  mounted() {
    console.log(this.$listeners) //即可拿到 change 事件
  }
  
  ```

- **`provide / inject` 适用于 隔代组件通信  ** ， provide 和 inject 绑定并不是可响应的。 

  ```
  // 父组件
  provide: function () {
    return {
      getMap: this.getMap
    }
  }
  // 子组件
  inject: ['getMap']
  ```

- .sync

  在 vue@1.x 的时候曾作为双向绑定功能存在，即子组件可以修改父组件中的值; 在 vue@2.0 的由于违背单项数据流的设计被干掉了; 在 vue@2.3.0+ 以上版本又重新引入了这个 .sync 修饰符;

```vue
// 父组件
<home :title.sync="title" />
//编译时会被扩展为
<home :title="title"  @update:title="val => title = val"/>

// 子组件
// 所以子组件可以通过$emit 触发 update 方法改变
mounted(){
  this.$emit("update:title", '这是新的title')
}
```



### vue2.0 数组赋值监听 

```js
// 索引修改某个数组项
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
// vm.$set，Vue.set的一个别名
vm.$set(vm.items, indexOfItem, newValue)
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

```js
// 修改数组长度
vm.items.splice(newLength)
```



### 在哪个生命周期内调用异步请求？

可以在钩子函数 created、beforeMount、mounted 中进行调用，因为在这三个钩子函数中，data 已经创建，可以将服务端端返回的数据进行赋值。但是本人推荐在 created 钩子函数中调用异步请求，因为在 created 钩子函数中调用异步请求有以下优点：

- 能更快获取到服务端数据，减少页面 loading 时间；
- ssr 不支持 beforeMount 、mounted 钩子函数，所以放在 created 中有助于一致性；

### 父组件可以监听到子组件的生命周期吗？

```vue
// Parent.vue
<Child @mounted="doSomething"/>
    
// Child.vue
mounted() {
  this.$emit("mounted");
}
```

```vue
//  Parent.vue
<Child @hook:mounted="doSomething" ></Child>

doSomething() {
   console.log('父组件监听到 mounted 钩子函数 ...');
},
    
//  Child.vue
mounted(){
   console.log('子组件触发 mounted 钩子函数 ...');
},    
    
// 以上输出顺序为：
// 子组件触发 mounted 钩子函数 ...
// 父组件监听到 mounted 钩子函数 ...     

```



### 自定义指令 (v-check, v-focus) 的方法有哪些？它有哪些钩子函数？还有哪些钩子函数参数

- 全局定义指令：在 vue 对象的 directive 方法里面有两个参数，一个是指令名称，另一个是函数。

- 组件内定义指令：directives

- 钩子函数：bind（绑定事件出发)、inserted（节点插入时候触发)、update（组件内相关更新)

- 钩子函数参数： el、binding

  

### 说出至少 4 种 vue 当中的指令和它的用法

v-if（判断是否隐藏)、v-for（把数据遍历出来)、v-bind（绑定属性)、v-model（实现双向绑定)

### 谈谈你对 keep-alive 的了解？

keep-alive 是 Vue 内置的一个组件，可以使被包含的组件保留状态，避免重新渲染 ，其有以下特性：

- 一般结合路由和动态组件一起使用，用于缓存组件；

- 提供 include 和 exclude 属性，两者都支持字符串或正则表达式，

   include 表示只有名称匹配的组件会被缓存，

   exclude 表示任何名称匹配的组件都不会被缓存 ，

   其中 exclude 的优先级比 include 高；

- 对应两个钩子函数 activated 和 deactivated ，当组件被激活时，触发钩子函数 activated，当组件被移除时，触发钩子函数 deactivated。

### 组件中 data 为什么是一个函数？

因为组件是用来复用的，且 JS 里对象是引用关系，如果组件中 data 是一个对象，那么这样作用域没有隔离，子组件中的 data 属性值会相互影响，如果组件中 data 选项是一个函数，那么每个实例可以维护一份被返回对象的独立的拷贝，组件实例之间的 data 属性值不会互相影响；而 new Vue 的实例，是不会被复用的，因此不存在引用对象的问题。