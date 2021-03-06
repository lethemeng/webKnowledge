<!-- TOC -->

- [布局](#布局)
  - [Flex 布局](#flex-布局)
  - [grid 网格布局](#grid-网格布局)
  - [三栏布局](#三栏布局)
    - [圣杯布局](#圣杯布局)
    - [双飞翼布局](#双飞翼布局)
    - [响应式设计和布局](#响应式设计和布局)

<!-- /TOC -->

# 布局

## Flex 布局

[Flex - 阮一峰 （语法篇）](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
[Flex - 阮一峰 （实战篇）](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

## grid 网格布局

[grid 网格布局](https://www.imooc.com/article/28513)

## 三栏布局

假设高度已知，请写出三栏布局，其中左右栏宽 300px, 中间自适应

1. 浮动-1

```html
<div class="wrap">
  <div class="left">左侧</div>
  <div class="right">右侧</div>
  <div class="middle">中间</div>
</div>
```

```css
.wrap {
  background: #eee;
  overflow: hidden;
  padding: 20px;
}
.left {
  width: 200px;
  height: 50px;
  float: left;
  background: coral;
}
.right {
  width: 120px;
  height: 200px;
  float: right;
  background: lightblue;
}

.middle {
  margin-left: 220px;
  margin-right: 140px;
  background: lightpink;
}
```

2. 绝对定位

```html
<div class="wrap">
  <div class="left">左侧</div>
  <div class="right">右侧</div>
  <div class="middle">中间</div>
</div>
```

```css
.wrap {
  position: relative;
  background: #eee;
}
.left {
  position: absolute;
  left: 0;
  top: 0;
  width: 200px;
  height: 50px;
  background: coral;
}
.right {
  position: absolute;
  right: 0;
  top: 0;
  width: 120px;
  height: 200px;
  background: lightblue;
}

.middle {
  height: 50px;
  margin-left: 220px;
  margin-right: 140px;
  background: lightpink;
}
```

![ab](../../img/三栏-ab.png)

1. 表格布局

```html
<style>
  .container {
    display: table;
    width: 100%;
  }
  .left,
  .main,
  .right {
    display: table-cell;
  }
  .left {
    width: 200px;
    height: 300px;
    background-color: red;
  }
  .main {
    background-color: blue;
  }
  .right {
    width: 100px;
    height: 300px;
    background-color: green;
  }
</style>
<div class="container">
  <div class="left"></div>
  <div class="main"></div>
  <div class="right"></div>
</div>
```

4. flex



```html
<div class="wrap">
  <div class="left">左侧</div>
  <div class="middle">中间</div>
  <div class="right">右侧</div>
</div>

<style type="text/css">
  .wrap {
    display: flex;
    justify-content: space-between;
  }
  .left,
  .right,
  .middle {
    height: 100px;
  }
  .left {
    width: 200px;
    /*flex: 0 0 12em*/
    background: coral;
  }
  .right {
    width: 120px;
    /*flex: 0 0 12em*/
    background: lightblue;
  }
  .middle {
    background: #555;
    width: 100%;
    /*flex: 1;*/
    margin: 0 20px;
  }
</style>
```

5. grid

6. float 浮动
   ```html
   <div class="container">
     <div class="main"></div>
     <div class="left"></div>
     <div class="right"></div>
   </div>
   ```
   ```css
   container {
     margin-left: 120px;
     margin-right: 220px;
   }
   .main {
     float: left;
     width: 100%;
     height: 300px;
     background-color: red;
   }
   .left {
     float: left;
     width: 200px;
     height: 300px;
     margin-left: -100%;
     position: relative;
     background-color: blue;
   }
   .right {
     float: left;
     width: 200px;
     height: 300px;
     margin-left: -200px;
     background-color: green;
   }
   ```

### 圣杯布局

要求：三列布局；中间宽度自适应，两边内容定宽。

好处：重要的内容放在文档流前面可以优先渲染

原理：利用相对定位、浮动、负边距布局，而不添加额外标签

实现方式：

main 部分首先要放在 container 的最前部分。然后是 left,right

1. 将三者都 float:left , 再加上一个 position:relative （因为相对定位后面会用到）

2.main 部分 width:100% 占满

3. 此时 main 占满了，所以要把 left 拉到最左边，使用 margin-left:-100%

4. 这时 left 拉回来了，但会覆盖 main 内容的左端，要把 main 内容拉出来，所以在外围 container 加上 padding:0 220px 0 200px

5.main 内容拉回来了，但 left 也跟着过来了，所以要还原，就对 left 使用相对定位 left:-200px 同理，right 也要相对定位还原 right:-220px

6. 到这里大概就自适应好了。如果想 container 高度保持一致可以给 left main right 都加上 min-height:130px

实现方式 2：

> `flex + order`

### 双飞翼布局

原理：主体元素上设置左右边距，预留两翼位置。左右两栏使用浮动和负边距归位。

左翅 left 有 200px, 右翅 right..220px.. 身体 main 自适应未知

1.html 代码中，main 要放最前边，left right

1. 将 main left right 都 float:left

2. 将 main 占满 width:100%

3. 此时 main 占满了，所以要把 left 拉到最左边，使用 margin-left:-100% 同理 right 使用 margin-left:-220px

（这时可以直接继续上边圣杯布局的步骤，也可以有所改动）

5.main 内容被覆盖了吧，除了使用外围的 padding，还可以考虑使用 margin。

给 main 增加一个内层 div-- main-inner, 然后 margin:0 220px 0 200px

### 响应式设计和布局

在不同设备上正常使用，一般主要处理屏幕大小问题

- 隐藏 + 折行 + 自适应空间
- rem 做单位
- viewport
  - width=divice-width,
- 媒体查询
