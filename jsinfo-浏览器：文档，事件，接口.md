# 浏览器：文档，事件，接口 

## 1. Document

### 1.1 浏览器环境，规格

主机环境提供了自己的对象和语言核心以外的函数。Web 浏览器提供了一种控制网页的方法。Node.JS 提供了服务器端功能，等等。

![image-20220322103547599](C:\Users\Opsuc\AppData\Roaming\Typora\typora-user-images\image-20220322103547599.png)

`window`根对象：

1. js代码的全局对象 
2. 代表浏览器窗口并提供了控制它的方法

`DOM`文档对象模型：

将所有页面内容表示为可以修改的对象。`document` 对象是页面的主要“入口点”。我们可以使用它来更改或创建页面上的任何内容。

`BOM`浏览器对象模型：

表示由浏览器（主机环境）提供的用于处理文档（`document`）之外的所有内容的其他对象。

函数 `alert/confirm/prompt` 也是 BOM 的一部分：它们与文档（document）没有直接关系，但它代表了与用户通信的纯浏览器方法。

---

### 1.2 DOM树

根据文档对象模型（DOM），每个 HTML 标签都是一个对象。嵌套的标签是闭合标签的“子标签（children）”。标签内的文本（包括换行符和空格）也是一个对象。所有这些对象都可以通过 JavaScript 来访问，我们可以使用它们来修改页面。

如果浏览器遇到格式不正确的 HTML，它会在形成 DOM 时自动更正它。

- 顶级标签总是  `<html>`
- 表格永远有 `<tbody>`
- 在生成 DOM 时，浏览器会自动处理文档中的错误，关闭标签等。

如果一些内容存在于 HTML 中，那么它也必须在 DOM 树中。**HTML 中的所有内容，甚至注释，都会成为 DOM 的一部分。**

---

### 1.3 遍历DOM

对 DOM 的所有操作都是以 `document` 对象开始。它是 DOM 的主“入口点”。从它我们可以访问任何节点。

- 最顶层的 document 节点是 `document.documentElement`。这是对应 `<html>` 标签的 DOM 节点。

- 另一个被广泛使用的 DOM 节点是 `<body>` 元素 — `document.body`。

- `<head>` 标签可以通过 `document.head` 访问。

脚本无法访问在运行时不存在的元素。（在`<head>`中运行的脚本访问 `document.body` 为null）

**在 DOM 的世界中，**`null` **就意味着“不存在”**



`childNodes` 集合（一个类数组的可迭代对象）列出了所有子节点，包括文本节点。

- **DOM 集合是只读的**
- **DOM 集合是实时的** 

`firstChild` 和 `lastChild` 属性是访问第一个和最后一个子元素的快捷方式。

`elem.hasChildNodes()` 用于检查节点是否有子节点。

下一个兄弟节点在 `nextSibling` 属性中，上一个是在 `previousSibling` 属性中。

可以通过 `parentNode` 来访问父节点。



只考虑元素节点 （`childNodes` 还包括了文本节点、注释节点）

- `children` —作为元素节点的子代的节点。
- `firstElementChild`，`lastElementChild` — 第一个和最后一个子元素。
- `previousElementSibling`，`nextElementSibling` — 兄弟元素。
- `parentElement` — 父元素。



```javascript
alert( document.documentElement.parentNode ); // document
alert( document.documentElement.parentElement ); // null
```

因为根节点 `document.documentElement`（`<html>`）的父节点是 `document`。但 `document` 不是一个元素节点，所以 `parentNode` 返回了 `document`，但 `parentElement` 返回的是 `null`。



某些类型的 DOM 元素，例如 table，提供了用于访问其内容的其他属性和集合。

---

### 1.4 搜索：getElement\*，querySelector\*

如果一个元素有 `id` 特性（attribute），那我们就可以使用 `document.getElementById(id)` 方法获取该元素。（`getElementById` 方法只能被在 `document` 对象上调用。它会在整个文档中查找给定的 `id`。）

`elem.querySelectorAll(css)`，返回 `elem` 中与给定 CSS 选择器匹配的所有元素。

`elem.querySelector(css)` 调用会返回`elem` 中给定 CSS 选择器的第一个元素。

[elem.matches(css)](http://dom.spec.whatwg.org/#dom-element-matches) 不会查找任何内容，它只会检查 `elem` 是否与给定的 CSS 选择器匹配。它返回 `true` 或 `false`。

`elem.closest(css)` 方法会查找与 CSS 选择器匹配的最近的祖先。`elem` 自己也会被搜索。

如果 `elemB` 在 `elemA` 内（`elemA` 的后代）或者 `elemA==elemB`，`elemA.contains(elemB)` 将返回 true。



所有的 `"getElementsBy*"` 方法都会返回一个 **实时的（live）** 集合。这样的集合始终反映的是文档的当前状态，并且在文档发生更改时会“自动更新”。

相反，`querySelectorAll` 返回的是一个 **静态的** 集合。

---

### 1.5 节点属性：type，tag和content

每个 DOM 节点都属于相应的内建类。

层次结构（hierarchy）的根节点是 [EventTarget](https://dom.spec.whatwg.org/#eventtarget)，[Node](http://dom.spec.whatwg.org/#interface-node) 继承自它，其他 DOM 节点继承自 Node。

![image-20220322214319933](C:\Users\Opsuc\AppData\Roaming\Typora\typora-user-images\image-20220322214319933.png)

`EventTarget `：是根的“抽象（abstract）”类。该类的对象从未被创建。它作为一个基础，以便让所有 DOM 节点都支持所谓的“事件（event）”，我们会在之后学习它。

`Node `：也是一个“抽象”类，充当 DOM 节点的基础。它提供了树的核心功能：`parentNode`，`nextSibling`，`childNodes` 等（它们都是 getter）。`Node` 类的对象从未被创建。但是有一些继承自它的具体的节点类，例如：文本节点的 `Text`，元素节点的 `Element`，以及更多异域（exotic）类，例如注释节点的 `Comment`。

`Element`：是 DOM 元素的基本类。它提供了元素级的导航（navigation），例如 `nextElementSibling`，`children`，以及像 `getElementsByTagName` 和 `querySelector` 这样的搜索方法。浏览器中不仅有 HTML，还会有 XML 和 SVG。`Element` 类充当更多特定类的基本类：`SVGElement`，`XMLElement` 和 `HTMLElement`。

`HTMLElement`— 最终是所有 HTML 元素的基本类。各种 HTML 元素均继承自它: `HTMLInputElement`, `HTMLBodyElement`, `HTMLAnchorElement`

**DOM 节点是常规的 JavaScript 对象。它们使用基于原型的类进行继承。**

对于 DOM 元素：

- `console.log(elem)` 显示元素的 DOM 树。
- `console.dir(elem)` 将元素显示为 DOM 对象，非常适合探索其属性。



给定一个 DOM 节点，我们可以从 `nodeName` 或者 `tagName` 属性（仅支持元素节点）中读取它的标签名



[innerHTML](https://w3c.github.io/DOM-Parsing/#the-innerhtml-mixin) 属性允许将元素中的 HTML 获取为字符串形式。我们也可以修改它。因此，它是更改页面最有效的方法之一。

如果 `innerHTML` 将一个 `<script>` 标签插入到 document 中 — 它会成为 HTML 的一部分，但是不会执行。



`outerHTML` 属性包含了元素的完整 HTML。就像 `innerHTML` 加上元素本身一样。**注意：与 `innerHTML` 不同，写入 `outerHTML` 不会改变元素。而是在 DOM 中替换它。**



`innerHTML` 属性仅对元素节点有效。其他节点类型，例如文本节点，具有它们的对应项：`nodeValue` 和 `data` 属性。这两者在实际使用中几乎相同，只有细微规范上的差异。因此，我们将使用 `data`，因为它更短。可以被修改。



`textContent` 提供了对元素内的 **文本** 的访问权限：仅文本，去掉所有 `<tags>`。

**写入 `textContent` 允许以“安全方式”写入文本。**



HTML “hidden” 特性（attribute）和 DOM 属性（property）（`elem.hidden = true`) 指定元素是否可见。执行与 CSS `display:none` 相同的事。

大多数标准 HTML 特性（attribute）都具有相应的 DOM 属性。但是 HTML 特性（attribute）和 DOM 属性（property）并不总是相同的。

DOM 节点还具有其他属性，具体有哪些属性则取决于它们的类。例如，`<input>` 元素（`HTMLInputElement`）支持 `value`，`type`，而 `<a>` 元素（`HTMLAnchorElement`）则支持 `href` 等。

`nodeType` : 我们可以使用它来查看节点是文本节点还是元素节点。它具有一个数值型值（numeric value）：`1` 表示元素，`3` 表示文本节点，其他一些则代表其他节点类型。只读。

---

### 1.6 特性和属性

当浏览器加载页面时，它会“解析”HTML 并从中生成 DOM 对象。对于元素节点，大多数**标准的** HTML 特性（attributes）会自动变成 DOM 对象的属性（properties）。

如果一个特性不是标准的，那么就没有相对应的 DOM 属性。那我们有什么方法来访问这些特性吗？有。所有特性都可以通过使用以下方法进行访问：

- `elem.hasAttribute(name)` — 检查特性是否存在。

- `elem.getAttribute(name)` — 获取这个特性值。

- `elem.setAttribute(name, value)` — 设置这个特性值。

- `elem.removeAttribute(name)` — 移除这个特性。

  可以使用 `elem.attributes` 读取所有特性（可迭代对象，具有`name`和`value`属性。）

特性的名称是大小写不敏感的。我们可以将任何东西赋值给特性，但是这些东西会被自动转换为字符串类型的。

当一个标准的特性被改变，对应的属性也会自动更新，（除了几个特例）反之亦然。

例如 `input.value` 只能从特性同步到属性，反过来则不行。（这个“功能”在实际中会派上用场，因为用户行为可能会导致 `value` 的更改，然后在这些操作之后，如果我们想从 HTML 中恢复“原始”值，那么该值就在特性中。）

DOM 属性不总是字符串类型的。例如，`input.checked` 属性（对于 checkbox 的）是布尔型的。而`input.getAttribute('checked')` 特性值为空字符串。

`style` 特性是字符串类型的，但 `style` 属性是一个对象。

即使一个 DOM 属性是字符串类型的，但它可能和 HTML 特性也是不同的。例如，`href` DOM 属性一直是一个 **完整的** URL，即使该特性包含一个相对路径或者包含一个 `#hash`。

###### 非标准的特性：dataset

为了避免冲突，存在 [data-*](https://html.spec.whatwg.org/#embedding-custom-non-visible-data-with-the-data-*-attributes) 特性。

**所有以 “data-” 开头的特性均被保留供程序员使用。它们可在 `dataset` 属性中读写。**

像 `data-order-state` 这样的多词特性可以以驼峰式进行调用：`dataset.orderState`。

使用 `data-*` 特性是一种合法且安全的传递自定义数据的方式。



在大多数情况下，最好使用 DOM 属性。仅当 DOM 属性无法满足开发需求，并且我们真的需要特性时，才使用特性，例如：

- 我们需要一个非标准的特性。但是如果它以 `data-` 开头，那么我们应该使用 `dataset`。
- 我们想要读取 HTML 中“所写的”值。对应的 DOM 属性可能不同，例如 `href` 属性一直是一个 **完整的** URL，但是我们想要的是“原始的”值。

---

### 1.7 修改文档 (document)

- 创建元素

  - `document.createElement(tag)`：用给定的标签创建一个新 **元素节点（element node）**
  - `document.createTextNode(text)`：用给定的文本创建一个 **文本节点**

- 元素插入方法

  这些方法的参数可以是一个要插入的任意的 DOM 节点列表，或者文本字符串（会被自动转换成文本节点）。

  - `node.append(...nodes or strings)` —— 在 `node` **末尾** 插入节点或字符串，
  - `node.prepend(...nodes or strings)` —— 在 `node` **开头** 插入节点或字符串，
  - `node.before(...nodes or strings)` —— 在 `node` **前面** 插入节点或字符串，
  - `node.after(...nodes or strings)` —— 在 `node` **后面** 插入节点或字符串，
  - `node.replaceWith(...nodes or strings)` —— 将 `node` 替换为给定的节点或字符串。

  这里的文字都被“作为文本”插入，而不是“作为 HTML 代码”。像 `<`、`>` 这样的符号都会被作转义处理来保证正确显示。换句话说，**字符串被以一种安全的方式插入到页面中**。

- 将内容作为HTML代码插入

  `elem.insertAdjacentHTML(where, html)`：该方法的第一个参数是代码字（code word），指定相对于 `elem` 的插入位置。必须为以下之一：

  - `"beforebegin"` — 将 `html` 插入到 `elem` 前插入，
  - `"afterbegin"` — 将 `html` 插入到 `elem` 开头，
  - `"beforeend"` — 将 `html` 插入到 `elem` 末尾，
  - `"afterend"` — 将 `html` 插入到 `elem` 后。

  第二个参数是 HTML 字符串，该字符串会被“作为 HTML” 插入。
  
- 移除节点

  - `node.remove()`

  - 如果我们要将一个元素 **移动** 到另一个地方，则无需将其从原来的位置中删除。**所有插入方法都会自动从旧位置删除该节点。**

    `second.after(first); // 获取 #second，并在其后面插入 #first`

- 克隆节点

  - `elem.cloneNode(true)`  创建元素的一个“深”克隆 — 具有所有特性（attribute）和子元素。
  -  `elem.cloneNode(false)` 克隆不包括子元素。

- `DocumentFragment`

  `DocumentFragment` 是一个特殊的 DOM 节点，用作来传递节点列表的包装器（wrapper）。

- `document.write()` 远古方法，已经很少使用

  **调用只在页面加载时工作**。如果我们在加载完成时调用它，则现有文档内容将被擦除。

  从技术上讲，当在浏览器正在读取（“解析”）传入的 HTML 时调用 `document.write` 方法来写入一些东西，浏览器会像它本来就在 HTML 文本中那样使用它。

  所以它运行起来出奇的快，因为它 **不涉及 DOM 修改**。它直接写入到页面文本中，而此时 DOM 尚未构建。

- 老式的插入删除方法，在parentElem上进行操作，如`appendChild`， `insertBefore`等。不如现在的方法方便灵活，不用了。

---

### 1.8 样式和类

通常有两种设置元素样式的方式：

1. 在 CSS 中创建一个类，并添加它：`<div class="...">`
2. 将属性直接写入 `style`：`<div style="...">`。

相较于将样式写入 `style` 属性，我们应该首选通过 CSS 类的方式来添加样式。仅当类“无法处理”时，才应选择使用 `style` 属性的方式。

`elem.className` 对应于 `"class"` 特性。如果我们对 `elem.className` 进行赋值，它将替换类中的整个字符串。

`elem.classList` 是一个特殊的对象，它具有 `add/remove/toggle` 单个类的方法。

- `elem.classList.add/remove(class)` — 添加/移除类。
- `elem.classList.toggle(class)` — 如果类不存在就添加类，存在就移除它。
- `elem.classList.contains(class)` — 检查给定类，返回 `true/false`。

此外，`classList` 是可迭代的。(`let of`)



`elem.style` 属性是一个对象，它对应于 `"style"` 特性（attribute）中所写的内容。对于多词（multi-word）属性，使用驼峰式 camelCase. 

像 `-moz-border-radius` 和 `-webkit-border-radius` 这样的浏览器前缀属性，也遵循同样的规则：连字符 `-` 表示大写。

重置单个样式属性：将属性值设置为空字符串。

完全重写某个元素的样式：

- 使用特殊属性 `style.cssText`：以字符串的形式设置完整的样式。
- 设置style特性值：`div.setAttribute('style', 'color: red...')`。

**`style` 属性仅对 `"style"` 特性（attribute）值起作用，而没有任何 CSS 级联（cascade）。** 因此我们无法使用 `elem.style` 读取来自 CSS 类的任何内容。

读取样式：`getComputedStyle(element, [pseudo])` 结果是一个具有样式属性的只读对象，属性值是样式的解析值。



注：JavaScript 看不到 `:visited` 所应用的样式。此外，CSS 中也有一个限制，即禁止在 `:visited` 中应用更改几何形状的样式。这是为了确保一个不好的页面无法测试链接是否被访问，进而窥探隐私。

---

### 1.9 元素大小和滚动

![image-20220323102504581](C:\Users\Opsuc\AppData\Roaming\Typora\typora-user-images\image-20220323102504581.png)

元素具有以下几何属性：

- `offsetParent` — 是最接近的 CSS 定位的祖先，或者是 `td`，`th`，`table`，`body`。
- `offsetLeft/offsetTop` — 是相对于 `offsetParent` 的左上角边缘的坐标。
- `offsetWidth/offsetHeight` — 元素的“外部” width/height，边框（border）尺寸计算在内。
- `clientLeft/clientTop` — 从元素左上角外角到左上角内角的距离。对于从左到右显示内容的操作系统来说，它们始终是左侧/顶部 border 的宽度。而对于从右到左显示内容的操作系统来说，垂直滚动条在左边，所以 `clientLeft` 也包括滚动条的宽度。
- `clientWidth/clientHeight` — 内容的 width/height，包括 padding，但不包括滚动条（scrollbar）。
- `scrollWidth/scrollHeight` — 内容的 width/height，就像 `clientWidth/clientHeight` 一样，但还包括元素的滚动出的不可见的部分。
- `scrollLeft/scrollTop` — 从元素的左上角开始，滚动出元素的上半部分的 width/height。

除了 `scrollLeft/scrollTop` 外，所有属性都是只读的。如果我们修改 `scrollLeft/scrollTop`，浏览器会滚动对应的元素。

---

### 1.10 Window大小和滚动

窗口的 width/height：`document.documentElement` 的 `clientWidth/clientHeight`

`window.innerWidth/innerHeight` 包括了滚动条。

获得当前滚动：`window.pageXOffset/pageYOffset`

通过更改 `scrollTop/scrollLeft` 来滚动常规元素。

更简单的通用解决方案：使用特殊方法 `window.scrollBy(x,y)` 和 `window.scrollTo(pageX,pageY)`。（相对坐标和绝对坐标）

`elem.scrollIntoView(top)` 的调用将滚动页面以使 `elem` 可见。它有一个参数：

- 如果 `top=true`（默认值），页面滚动，使 `elem` 出现在窗口顶部。元素的上边缘将与窗口顶部对齐。
- 如果 `top=false`，页面滚动，使 `elem` 出现在窗口底部。元素的底部边缘将与窗口底部对齐。

要使文档不可滚动，只需要设置 `document.body.style.overflow = "hidden"`。该页面将“冻结”在其当前滚动位置上。

这个方法的缺点是会使滚动条消失。如果滚动条占用了一些空间，它原本占用的空间就会空出来，那么内容就会“跳”进去以填充它。

---

### 1.11 坐标

大多数 JavaScript 方法处理的是以下两种坐标系中的一个：

1. 相对于窗口

   — 类似`position:fixed`，从窗口的顶部/左侧边缘计算得出。

   - 我们将这些坐标表示为 `clientX/clientY`

2. 相对于文档
   — 与文档根（document root）中的`position:absolute`类似，从文档的顶部/左侧边缘计算得出。

   - 我们将它们表示为 `pageX/pageY`。

这两个坐标系统通过以下公式相连接：

- `pageY` = `clientY` + 文档的垂直滚动出的部分的高度。
- `pageX` = `clientX` + 文档的水平滚动出的部分的宽度。

页面上的任何点都有坐标：

1. 相对于窗口的坐标 — `elem.getBoundingClientRect()`。
2. 相对于文档的坐标 — `elem.getBoundingClientRect()` 加上当前页面滚动（`window.pageXOffset/pageYOffset`）。

这些函数的结果是没有单位的，使用时需要自己加上`"px"`

---

## 2. 事件简介

