## Flex

弹性盒子由弹性容器(Flex container)和弹性子元素(Flex item)组成。

### 弹性容器(Flex container)

弹性容器通过设置 display 属性的值为 flex 或 inline-flex将其定义为弹性容器。

1、`flex-direction`: 属性指定了弹性子元素在父容器中的位置.

- row：横向从左到右排列（左对齐），默认的排列方式。
- row-reverse：反转横向排列（右对齐，从后往前排，最后一项排在最前面。
- column：纵向排列。
- column-reverse：反转纵向排列，从后往前排，最后一项排在最上面。

2、`justify-content `:内容对齐（justify-content）属性应用在弹性容器上，把弹性项沿着弹性容器的主轴线（main axis）对齐。

- flex-start：

  弹性项目向行头紧挨着填充。这个是默认值。第一个弹性项的main-start外边距边线被放置在该行的main-start边线，而后续弹性项依次平齐摆放。

- flex-end：

  弹性项目向行尾紧挨着填充。第一个弹性项的main-end外边距边线被放置在该行的main-end边线，而后续弹性项依次平齐摆放。

- center：

  弹性项目居中紧挨着填充。（如果剩余的自由空间是负的，则弹性项目将在两个方向上同时溢出）。

- space-between：

  弹性项目平均分布在该行上。如果剩余空间为负或者只有一个弹性项，则该值等同于flex-start。否则，第1个弹性项的外边距和行的main-start边线对齐，而最后1个弹性项的外边距和行的main-end边线对齐，然后剩余的弹性项分布在该行上，相邻项目的间隔相等。

- space-around：

  弹性项目平均分布在该行上，两边留有一半的间隔空间。如果剩余空间为负或者只有一个弹性项，则该值等同于center。否则，弹性项目沿该行分布，且彼此间隔相等（比如是20px），同时首尾两边和弹性容器之间留有一半的间隔（1/2*20px=10px）。

![img](../img/2259AD60-BD56-4865-8E35-472CEABF88B2.jpg)



3、`align-items `: 设置或检索弹性盒子元素在侧轴（纵轴）方向上的对齐方式。

- flex-start：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴起始边界。
- flex-end：弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴结束边界。
- center：弹性盒子元素在该行的侧轴（纵轴）上居中放置。（如果该行的尺寸小于弹性盒子元素的尺寸，则会向两个方向溢出相同的长度）。
- baseline：如弹性盒子元素的行内轴与侧轴为同一条，则该值与'flex-start'等效。其它情况下，该值将参与基线对齐。
- stretch：如果指定侧轴大小的属性值为'auto'，则其值会使项目的边距盒的尺寸尽可能接近所在行的尺寸，但同时会遵照'min/max-width/height'属性的限制。

4、`flex-wrap `: 属性用于指定弹性盒子的子元素换行方式。

- **nowrap** - 默认， 弹性容器为单行。该情况下弹性子项可能会溢出容器。
- **wrap** - 弹性容器为多行。该情况下弹性子项溢出的部分会被放置到新行，子项内部会发生断行
- **wrap-reverse** -反转 wrap 排列。

5、`align-content `: 属性用于修改 `flex-wrap` 属性的行为。类似于 `align-items`, 但它不是设置弹性子元素的对齐，而是设置各个行的对齐。

- `stretch` - 默认。各行将会伸展以占用剩余的空间。
- `flex-start` - 各行向弹性盒容器的起始位置堆叠。
- `flex-end` - 各行向弹性盒容器的结束位置堆叠。
- `center` -各行向弹性盒容器的中间位置堆叠。
- `space-between` -各行在弹性盒容器中平均分布。
- `space-around` - 各行在弹性盒容器中平均分布，两端保留子元素与子元素之间间距大小的一半。

### 弹性子元素(Flex item)

1、`order`: 排序

<integer>：用整数值来定义排列顺序，数值小的排在前面。可以为负值。

对齐

2、`flex`属性：

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

### [Flex Basis与Width的区别](https://www.jianshu.com/p/17b1b445ecd4)

Flex items 的应用准则

**content –> width –> flex-basis (limted by max|min-width)**

- 如果没有设置flex-basis属性，那么flex-basis的大小就是项目的width属性的大小
- 如果没有设置width属性，那么flex-basis的大小就是项目内容(content)的大小
-  flex-base 可以使用min-width max-width来限制