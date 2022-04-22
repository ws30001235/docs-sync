## CSS面试题

#### 1. 解释 `position`

- static
- relative
- absolute
- fixed
- sticky （关于层叠上下文的知识）

#### 2. `block`和`inline`元素的不同

https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flow_Layout

https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flow_Layout/Intro_to_formatting_contexts

#### 3. 解释盒模型

盒子分为块级盒子和内联盒子。

块级盒子：每个盒子都会换行，width 和 height 设置后有用，内边距（padding）, 外边距（margin） 和 边框（border） 会将其他元素从当前盒子周围“推开”。

内联盒子：不会换行，width 和 height 不起作用。垂直方向的内边距、外边距和边框会应用但是不会占据空间（不会把其他内联盒子推开）。



盒子从内到外是内容，内边距，边框和外边距。

https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model

#### 4. 什么是伪类，什么是伪元素

#### 5. 在CSS中使用@import的优缺点是什么

https://www.keycdn.com/blog/css-import

#### 6. 如何创建一个具有固定比例的盒子

responsive

#### 7. 什么是 DPR， CSS像素和设备像素分别是什么

CSS像素

设备像素是设备具有的像素点。

**DPR = 设备像素/CSS像素**

![img](https://www.minidiy.com/upload/20201216_15242135.png)

如图右是DPR为2的retina屏幕，需要4个设备像素显示一个CSS像素。