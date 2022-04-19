### CSS

##### 图形

###### 图形加载：

- 使用 Data URL

  格式`data:[<mime type>][;charset=][;base64],<encoded data>`

  优点是可以减少HTTP请求，缺点是相较于直接用图片，更难维护，使得文件的体积变大

###### 使用图形：

- 用border,伪元素等技巧 https://css-tricks.com/the-shapes-of-css/
- 用svg
- 用`clip-path`特性



###### 可替换元素

在 [CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS) 中，**可替换元素**（**replaced element**）的展现效果不是由 CSS 来控制的。这些元素是一种外部对象，它们外观的渲染，是独立于 CSS 的。

简单来说，它们的内容不受当前文档的样式的影响。CSS 可以影响可替换元素的位置，但不会影响到可替换元素自身的内容。某些可替换元素，例如 [``](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/iframe) 元素，可能具有自己的样式表，但它们不会继承父文档的样式。

典型的可替换元素有：

- `<iframe>`
- `<video>`
- `<embed>`
- `<img>`



###### `object-fit`属性

对于`img`标签这种可替换元素来说，当我们不指定元素的宽高，元素的宽高将根据原图的宽高计算得出；当我们只指定元素的`width`，会根据原图的宽高比例计算出`height`；如果我们指定了元素的`width`和`height`但比例和原图比例不一致时，图片就会自适应的进行拉伸，此时`object-fit`默认值为`fill`

我们可以通过修改`object-fit`来调整图片适应宽高的策略。

- `object-fit: fill` 填充，因此图片会被拉伸或压缩
- `object-fit: cover`填充，同时保证图片比例不变，因此图片会被裁剪
- `object-fit: contain`容纳图片，因此元素两侧会出现空白区域



###### `background-clip`属性

`background-clip` 设置元素的背景（背景图片或颜色）是否延伸到边框、内边距盒子、内容盒子下面。

设置为text，再将color设置为transparent就是用图片填充文本。

##### 变换

- `transform` scale(), translate(), skew(), rotate(),

- `transition` 过渡。提供了一种在更改CSS属性时控制动画速度的方法。 其可以让属性变化成为一个持续一段时间的过程，而不是立即生效的。

  | [transition](http://www.runoob.com/cssref/css3-pr-transition.html) | 简写属性，用于在一个属性中设置四个过渡属性。 |
  | ------------------------------------------------------------ | -------------------------------------------- |
  | [transition-property](http://www.runoob.com/cssref/css3-pr-transition-property.html) | 规定应用过渡的 CSS 属性的名称。              |
  | [transition-duration](http://www.runoob.com/cssref/css3-pr-transition-duration.html) | 定义过渡效果花费的时间。默认是 0。           |
  | [transition-timing-function](http://www.runoob.com/cssref/css3-pr-transition-timing-function.html) | 规定过渡效果的时间曲线。默认是 "ease"。      |
  | [transition-delay](http://www.runoob.com/cssref/css3-pr-transition-delay.html) | 规定过渡效果何时开始。默认是 0。             |

- `animation` @keyframes 创建动画样式

  animation (简写属性）执行动画

##### 继承

继承属性没有指定的值时，取父元素同属性的计算值。只有文档根元素取该属性的概述中给定的[初始值](https://developer.mozilla.org/zh-CN/docs/Web/CSS/initial_value)（[initial value](https://developer.mozilla.org/zh-CN/docs/Web/CSS/initial_value)）。非继承属性没有指定值时，取属性的初始值。

`revert` 继承属性就是继承自父元素的属性值，非继承属性值为用户代理样式表的值

CSS 的 shorthand 属性 `all` 可以用于同时将这些继承值中的一个应用于（几乎）所有属性。它的值可以是其中任意一个(`inherit`, `initial`, `unset`, or `revert`)。这是一种撤销对样式所做更改的简便方法，以便回到之前已知的起点。

##### 层叠

相互冲突的声明将按以下顺序适用，后一种声明将覆盖前一种声明：

1. 用户代理样式表中的声明(例如，浏览器的默认样式，在没有设置其他样式时使用)。
2. 用户样式表中的常规声明(由用户设置的自定义样式)。
3. 作者样式表中的常规声明(这些是我们web开发人员设置的样式)。
4. 作者样式表中的`!important`声明
5. 用户样式表中的`!important` 声明

对于web开发人员的样式表来说，覆盖用户样式表是有意义的，因此设计可以按预期进行，但是有时用户充足的理由覆盖web开发人员样式，正如上面提到的—这可以通过在他们的规则中使用`!important`来实现。



影响层叠的三个因素（前面的更重要）：

1. 重要程度 (!important)
2. 优先级
3. 资源顺序

##### 文本与换行

对于浏览器的文本，不同语言的文本在换行时采用的规则不一样。根据换行时的行为差异，可以把文本分为两类：

1. CJK文本，即中文、日文和韩文
2. Non-CJK文本，比如我们用的最多的英文

在默认情况下，即`word-wrap: normal`, `word-break: normal`, `white-space: normal`时，CJK文本可以在任意2个字符间断行，而英文只能在空白符处断行。



---

###### 行内元素的间隙

父级元素设置 `font-size:0`解决子代的inline或inlien-block元素的空隙问题。

也可以在子代元素html标签之间的结束符号>和开始符号<写在同一行。



滚动条在盒模型的padding那个空间里。



##### layout

多列布局

```css
.items {
	column-count: 2;
	column-gap: 1em;
}
```

用`column-width`定义最小宽度，而不指定列数，这样的布局是响应式的。

```css
.items {
	width: 100%;
	column-width: 260px;
	column-gap: 1em;
}
```

