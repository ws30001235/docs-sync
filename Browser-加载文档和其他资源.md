## 五、加载文档和其他资源

### 5.1 页面生命周期

知识点：

HTML 页面的生命周期包含三个重要事件：

- `DOMContentLoaded` —— 浏览器已完全加载 HTML，并构建了 DOM 树，但像 `<img>` 和样式表之类的外部资源可能尚未加载完成。
- `load` —— 浏览器不仅加载完成了 HTML，还加载完成了所有外部资源：图片，样式等。
- `beforeunload/unload` —— 当用户正在离开页面时。

每个事件都是有用的：

- `DOMContentLoaded` 事件 ——**DOM 已经就绪**，因此处理程序可以查找 DOM 节点，并初始化接口。
- `load` 事件 —— 外部资源已加载完成，样式已被应用，图片大小也已知了。
- `beforeunload` 事件 —— 用户正在离开：我们可以检查用户是否保存了更改，并询问他是否真的要离开。
- `unload` 事件 —— 用户几乎已经离开了，但是我们仍然可以启动一些操作，例如发送统计数据。

当浏览器处理一个 HTML 文档，并在文档中遇到 `<script>` 标签时，就会在继续构建 DOM 之前运行它。（阻塞 `DOMContentLoaded` 的脚本）

+ 此规则有两个例外：
1. 具有 `async` 特性（attribute）的脚本不会阻塞 `DOMContentLoaded`，[稍后](https://zh.javascript.info/script-async-defer) 我们会讲到。
2. 使用 `document.createElement('script')` 动态生成并添加到网页的脚本也不会阻塞 `DOMContentLoaded`。

外部样式表不会影响 DOM，因此 `DOMContentLoaded` 不会等待它们。但这里有一个陷阱。如果在样式后面有一个脚本，那么该脚本必须等待样式表加载完成。

Firefox，Chrome 和 Opera 都会在 `DOMContentLoaded` 中自动填充表单。

`DOMContentLoaded` 事件发生在 `document` 对象上。我们必须使用 `addEventListener` 来捕获它。

当整个页面，包括样式、图片和其他资源被加载完成时，会触发 `window` 对象上的 `load` 事件。可以通过 `onload` 属性获取此事件。

如果访问者触发了离开页面的导航（navigation）或试图关闭窗口，`beforeunload` 处理程序将要求进行更多确认。

当访问者离开页面时，`window` 对象上的 `unload` 事件就会被触发。我们可以在那里做一些不涉及延迟或询问用户的操作，例如关闭相关的弹出窗口，发送分析数据。

`document.readyState` 属性可以为我们提供当前文档加载状态的信息。可以在 `readystatechange` 事件中跟踪状态更改。

+ `readyState`有 3 个可能值：
  
  + `loading` —— 文档正在被加载。
  
  + `interactive` —— 文档被全部读取。（DOMContentLoaded 几乎同时发生）
  
  + `complete` —— 文档被全部读取，并且所有资源（例如图片等）都已加载完成。

所以，我们可以检查 `document.readyState` 并设置一个处理程序，或在代码准备就绪时立即执行它。

---

### 5.2 脚本：async，defer

##### 1. defer

`defer` 特性告诉浏览器不要等待脚本。相反，浏览器将继续处理 HTML，构建 DOM。脚本会“在后台”下载，然后等 DOM 构建完成后，脚本才会执行。

- 具有 `defer` 特性的脚本不会阻塞页面。
- 具有 `defer` 特性的脚本总是要等到 DOM 解析完毕，但在 `DOMContentLoaded` 事件之前执行。

**具有 `defer` 特性的脚本保持其相对顺序**。

`defer` 特性仅适用于外部脚本。如果 `<script>` 脚本没有 `src`，则会忽略 `defer` 特性。

在实际开发中，`defer` 用于需要整个 DOM 的脚本，和/或脚本的相对执行顺序很重要的时候。

##### 2. async

`async` 脚本会在后台加载，并在加载就绪时运行。DOM 和其他脚本不会等待它们，它们也不会等待其它的东西。`async` 脚本就是一个会**在加载完成时执行**的完全独立的脚本。

- 浏览器不会因 `async` 脚本而阻塞（与 `defer` 类似）。
- 其他脚本不会等待 `async` 脚本加载完成，同样，`async` 脚本也不会等待其他脚本。
- `DOMContentLoaded` 和异步脚本不会彼此等待：
  - `DOMContentLoaded` 可能会发生在异步脚本之前（如果异步脚本在页面完成后才加载完成）
  - `DOMContentLoaded` 也可能发生在异步脚本之后（如果异步脚本很短，或者是从 HTTP 缓存中加载的）

当我们将独立的第三方脚本集成到页面时，此时采用异步加载方式是非常棒的：计数器，广告等，因为它们不依赖于我们的脚本，我们的脚本也不应该等待它们。

##### 3. 动态脚本

我们可以使用 JavaScript 动态地创建一个脚本，并将其附加（append）到文档（document）中：

```javascript
let script = document.createElement('script');

script.src = "/article/script-async-defer/long.js";

document.body.append(script);
```

**默认情况下，动态脚本的行为是“异步”的。**

- 它们不会等待任何东西，也没有什么东西会等它们。
- 先加载完成的脚本先执行（“加载优先”顺序）。

如果我们显式地设置了 `script.async=false`，则可以改变这个规则。然后脚本将按照脚本在文档中的顺序执行，就像 `defer` 那样。

---

### 资源加载：onload, onerror

#### script.onload

`load` 事件会在脚本加载并执行完成时触发。因此，在 `onload` 中我们可以使用脚本中的变量，运行函数等。

#### script.onerror

发生在脚本加载期间的 error 会被 `error` 事件跟踪到。

#### 其他资源

`load` 和 `error` 事件也适用于其他资源，基本上（basically）适用于具有外部 `src` 的任何资源。

- 大多数资源在被添加到文档中后，便开始加载。但是 `<img>` 是个例外。它要等到获得 src `(*)` 后才开始加载。
- 对于 `<iframe>` 来说，iframe 加载完成时会触发 `iframe.onload` 事件，无论是成功加载还是出现 error。

#### 跨源策略

来自一个网站的脚本无法访问其他网站的内容。或者，更确切地说，一个源（域/端口/协议三者）无法获取另一个源（origin）的内容。

**要允许跨源访问，`<script>` 标签需要具有 `crossorigin` 特性（attribute），并且远程服务器必须提供特殊的 header。**

这里有三个级别的跨源访问：

1. **无 `crossorigin` 特性** —— 禁止访问。
2. **`crossorigin="anonymous"`** —— 如果服务器的响应带有包含 `*` 或我们的源（origin）的 header `Access-Control-Allow-Origin`，则允许访问。浏览器不会将授权信息和 cookie 发送到远程服务器。
3. **`crossorigin="use-credentials"`** —— 如果服务器发送回带有我们的源的 header `Access-Control-Allow-Origin` 和 `Access-Control-Allow-Credentials: true`，则允许访问。浏览器会将授权信息和 cookie 发送到远程服务器。
