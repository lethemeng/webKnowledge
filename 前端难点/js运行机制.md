## JavaScript 运行机制

> [JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

### 1、为什么JavaScript是单线程？

JavaScript 最大的特点就是单线程，同一时间内只能做一件事。原因在于作为浏览器脚本语言，JavaScript是用于与用户互动，操作DOM的。这使得它只能是单线程，否则将会带来复杂的同步问题。

**多线程能力**

为了利用cpu多核的计算能力，HTML5提出了Web Worker标准，允许JavaScript创建多个线程，但是子线程完全受控于主线程，并且不能操作DOM。

### 2、浏览器多进程

### 3、任务队列

### 4、事件和回调函数

### 5、Event Loop

![Event Loop](../img/bg2014100802.png)

### 6、宏任务和微任务



## JavaScript 执行上下文和执行栈

> [[译] 理解 JavaScript 中的执行上下文和执行栈](https://juejin.im/post/5ba32171f265da0ab719a6d7)
>
> https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0

### 1、什么是执行上下文(Execution Context, 缩写 EC)？

执行上下文是JavaScript代码评估和执行环境的抽象概念。JavaScript代码运行时，就是在执行上下文中运行的。

#### 执行上下文的类型

- **全局执行上下文（Global Execution Context）**是默认或者基本的上下文，任何不在函数内部的代码都在全局执行上下文中。它会执行两件事：创建一个全局的window对象，并设置this为全局对象。一个程序中只会有一个全局上下文。
- **函数执行上下文（Function Execution Context）**当函数执行时都会为该函数创建一个新的上下文，每个函数都有自己的执行上下文，在函数被调用时会创建该函数的执行上下文。函数上下文可以有多个。
- Eval 函数执行上下文（Eval Execution Context） 在eval函数内部会有属于自己的执行上下文。

### 执行栈

执行栈，也就是其他编程语言中所说的“调用栈”，是一种LIFO(Last in, First out，先进后出)的结构，这里存储的是在代码运行时

当 JavaScript 引擎第一次遇到你的脚本时，它会创建一个全局的执行上下文并且压入当前执行栈。每当引擎遇到一个函数调用，它会为该函数创建一个新的执行上下文并压入栈的顶部。

引擎会执行那些执行上下文位于栈顶的函数。当该函数执行结束时，执行上下文从栈中弹出，控制流程到达当前栈中的下一个上下文。

```js
let a = 'Hello World!';
function first() {
  console.log('Inside first function');
  second();
  console.log('Again inside first function');
}
function second() {
  console.log('Inside second function');
}
first();
console.log('Inside Global Execution Context');
```

![1_ACtBy8CIepVTOSYcVwZ34Q](../img/1_ACtBy8CIepVTOSYcVwZ34Q.png)

上述代码的执行上下文栈。

当上述代码在浏览器加载时，JavaScript 引擎创建了一个全局执行上下文并把它压入当前执行栈。当遇到 `first()` 函数调用时，JavaScript 引擎为该函数创建一个新的执行上下文并把它压入当前执行栈的顶部。

当从 `first()` 函数内部调用 `second()` 函数时，JavaScript 引擎为 `second()` 函数创建了一个新的执行上下文并把它压入当前执行栈的顶部。当 `second()` 函数执行完毕，它的执行上下文会从当前栈弹出，并且控制流程到达下一个执行上下文，即 `first()` 函数的执行上下文。

当 `first()` 执行完毕，它的执行上下文从栈弹出，控制流程到达全局执行上下文。一旦所有代码执行完毕，JavaScript 引擎从当前栈中移除全局执行上下文。

### 2、怎么创建执行上下文？

到现在，我们已经看过 JavaScript 怎样管理执行上下文了，现在让我们了解 JavaScript 引擎是怎样创建执行上下文的。

创建执行上下文有两个阶段：**1) 创建阶段** 和 **2) 执行阶段**。

#### 创建阶段（The Creation Phase）

执行上下文创建阶段会生成三个对象：

1、绑定 this

2、创建词法环境（**LexicalEnvironment** ）

3、创建变量环境（**VariableEnvironment**）

```js
ExecutionContext = {
  ThisBinding = <this value>,
  LexicalEnvironment = { ... },
  VariableEnvironment = { ... },
}
```

##### this bind

全局执行上下文，this -> 全局对象，浏览器中指向window

在函数执行上下文中，this的值取决于函数是如何被调用的

##### 词法环境

> **词法环境**是一种规范类型，基于 ECMAScript 代码的词法嵌套结构来定义**标识符**和具体变量和函数的关联。一个词法环境由环境记录器和一个可能的引用**外部**词法环境的空值组成.——[官方的 ES6](https://link.juejin.im/?target=http%3A%2F%2Fecma-international.org%2Fecma-262%2F6.0%2F) 文档

简单来说**词法环境**是一种持有**标识符—变量映射**的结构。（这里的**标识符**指的是变量/函数的名字，而**变量**是对实际对象[包含函数类型对象]或原始数据的引用）。

词法环境的**内部**有两个组件：(1) **环境记录器**和 (2) 一个**外部环境的引用**。

1. **环境记录器**是存储变量和函数声明的实际位置。
2. **外部环境的引用**意味着它可以访问其父级词法环境（作用域）。

**词法环境**有两种类型：

- **全局环境**（在全局执行上下文中）是没有外部环境引用的词法环境。全局环境的外部环境引用是 **null**。它拥有内建的 Object/Array/等、在环境记录器内的原型函数（关联全局对象，比如 window 对象）还有任何用户定义的全局变量，并且 `this`的值指向全局对象。
- 在**函数环境**中，函数内部用户定义的变量存储在**环境记录器**中。并且引用的外部环境可能是全局环境，或者任何包含此内部函数的外部函数。

**环境记录器**也有两种类型（如上！）：

1. **声明式环境记录器**存储变量、函数和参数。
2. **对象环境记录器**用来定义出现在**全局上下文**中的变量和函数的关系。

简而言之，

- 在**全局环境**中，环境记录器是对象环境记录器。
- 在**函数环境**中，环境记录器是声明式环境记录器。

**注意 —** 对于**函数环境**，**声明式环境记录器**还包含了一个传递给函数的 `arguments` 对象（此对象存储索引和参数的映射）和传递给函数的参数的 **length**。

抽象地讲，词法环境在伪代码中看起来像这样：

```
GlobalExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 在这里绑定标识符
    }
    outer: <null>
  }
}

FunctionExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 在这里绑定标识符
    }
    outer: <Global or outer function environment reference>
  }
}

```

##### 变量环境：

它同样是一个词法环境，其环境记录器持有**变量声明语句**在执行上下文中创建的绑定关系。

如上所述，变量环境也是一个词法环境，所以它有着上面定义的词法环境的所有属性。

在 ES6 中，**词法环境**组件和**变量环境**的一个不同就是前者被用来存储函数声明和变量（`let` 和 `const`）绑定，而后者只用来存储 `var` 变量绑定。

我们看点样例代码来理解上面的概念：

```js
let a = 20;
const b = 30;
var c;

function multiply(e, f) {
 var g = 20;
 return e * f * g;
}

c = multiply(20, 30);

```

执行上下文看起来像这样：

```js
GlobalExectionContext = {

  ThisBinding: <Global Object>,

  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 在这里绑定标识符
      a: < uninitialized >,
      b: < uninitialized >,
      multiply: < func >
    }
    outer: <null>
  },

  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 在这里绑定标识符
      c: undefined,
    }
    outer: <null>
  }
}

FunctionExectionContext = {
  ThisBinding: <Global Object>,

  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 在这里绑定标识符
      Arguments: {0: 20, 1: 30, length: 2},
    },
    outer: <GlobalLexicalEnvironment>
  },

VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 在这里绑定标识符
      g: undefined
    },
    outer: <GlobalLexicalEnvironment>
  }
}

```

**注意** — 只有遇到调用函数 `multiply` 时，函数执行上下文才会被创建。

可能你已经注意到 `let` 和 `const` 定义的变量并没有关联任何值，但 `var` 定义的变量被设成了 `undefined`。

这是因为在创建阶段时，引擎检查代码找出变量和函数声明，虽然函数声明完全存储在环境中，但是变量最初设置为 `undefined`（`var` 情况下），或者未初始化（`let` 和 `const` 情况下）。

这就是为什么你可以在声明之前访问 `var` 定义的变量（虽然是 `undefined`），但是在声明之前访问 `let` 和 `const` 的变量会得到一个引用错误。

这就是我们说的变量声明提升。

#### 执行阶段

这是整篇文章中最简单的部分。在此阶段，完成对所有这些变量的分配，最后执行代码。

**注意** — 在执行阶段，如果 JavaScript 引擎不能在源码中声明的实际位置找到 `let` 变量的值，它会被赋值为 `undefined`。