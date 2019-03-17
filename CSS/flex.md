### flex

`flex`属性：

作用：是`flex-grow`,`flex-shrink`和`flex-basis`的简写，后两个属性可选。

- `flex-grow : <number>` : 定义项目的伸缩比例，按照该比例分配空间，数值越大占据空间越大，默认为 0
- `flex-shrink : <number>` : 定义项目的收缩比例，按照该比例分配空间，数值越大，占据空间越小，默认为 0
- `flex-basis : <length> | auto`: 定义在分配多余空间时项目占据主轴的空间，浏览器根据这个属性计算主轴是否有剩余空间 默认是 auto ，就是原始尺寸，也可以设置定值，让项目具有固定空间

`flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ];`

> - 0 1 auto: 默认不伸缩，如果容器空间不足等比例缩放
> - 1 1 auto： 对应关键字 **auto** ，如果容器有多余空间，则等比例分配多余空间，如果不足，则等比例缩放
> - 0 0 auto ： 对应关键字 **none** 按项目原始大小分配

### 设为 flex 属性之后，子元素的哪些属性会失效

`float clear vertical-align`
