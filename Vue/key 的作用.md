##  为何写列表组件的时候需要 key

在日常的开发中我们经常会需要为v-for渲染的列表指定key，在React中key是必须的，vue 中不写的key的话会有一条警告信息。key 到底有什么作用呢？

首先我们需要了解的VNode的概念，VNode是虚拟DOM用于描述真正DOM的数据结构，VNode结构大致如下图所示：



我们都知道Vue和React都是采用的diff 算法进行的节点更新，diff 算法的核心在与如何快速高效的找出两个VNode之间的差异，从而最小化的操作DOM，更新页面。

Vue 中判断两个VNode是否是同一节点的



