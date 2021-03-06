# 浏览器, WebAPI-面试题

### 1.浏览器跨域和`CORS`

##### 知识点：

1. **源（origin）**：域（domain）/端口（port）/协议（protocol）的组合。

2. 一个 origin 由**协议（Protocol）**、**主机名（Host）**和**端口（Port）**组成，这三块也是同源策略的判定条件，只有当**协议**、**主机名**和**端口**都相同时，浏览器才判定两者是同源关系，否则即为跨域。

3. 同源策略是浏览器一个非常重要的安全策略，基于这个策略可以限制非同源的内容与当前页面进行交互，从而减少页面被攻击的可能性。

4.  `CORS`：跨源资源共享（Cross-Origin Resource Sharing）是目前最为广泛的解决跨域问题的方案。CORS 是 [HTTP](https://developer.mozilla.org/zh-CN/docs/Glossary/HTTP) 的一部分，它允许服务端来指定哪些主机可以从这个服务端加载资源。

5. 从浏览器角度来看，有两种跨源请求：“简单”请求和其他请求。

   - [简单请求](http://www.w3.org/TR/cors/#terminology) 必须满足下列条件：
     - 方法：GET，POST 或 HEAD。
     - header —— 我们仅能设置：
       - `Accept` `Accept-Language` `Content-Language`
       -  `Content-Type` 的值为 `application/x-www-form-urlencoded`，`multipart/form-data` 或 `text/plain`。
   - 对于简单请求，浏览器会使用 `Origin` header 并立即发送，而对于其他请求，浏览器会发出初步的“预检”请求，以请求许可。
   - 对于非简单请求，会在请求之前发出初步“预检”请求。浏览器会自动向服务端发送一个 **OPTIONS** 请求，通过服务端返回的 `Access-Control-Allow-*` 判定请求是否被允许。

6. 要允许跨源访问，`<script>` 标签需要具有 `crossorigin` 特性（attribute），并且远程服务器必须提供特殊的 header。

   这里有三个级别的跨源访问：

   1. **无 `crossorigin` 特性** —— 禁止访问。
   2. **`crossorigin="anonymous"`** —— 如果服务器的响应带有包含 `*` 或我们的源（origin）的 header `Access-Control-Allow-Origin`，则允许访问。浏览器不会将授权信息和 cookie 发送到远程服务器。
   3. **`crossorigin="use-credentials"`** —— 如果服务器发送回带有我们的源的 header `Access-Control-Allow-Origin` 和 `Access-Control-Allow-Credentials: true`，则允许访问。浏览器会将授权信息和 cookie 发送到远程服务器。

7. **反向代理**：反向代理解决跨域问题的方案依赖同源的服务端对请求做一个转发处理，将请求从跨域请求转换成同源请求。反向代理的实现方式为在页面同域下配置一套反向代理服务，页面请求同域的服务端，服务端请求上游的实际的服务端，之后将结果返回给前端。

8. `JSONP`：JSONP 是一个相对古老的跨域解决方案。主要是利用了浏览器加载 JavaScript 资源文件时不受同源策略的限制而实现跨域获取数据。

9. 浏览器允许跨域资源嵌入，如script, css, images, iframes, multimedia。浏览器允许将跨域的URL作为form元素的action特性值，跨域写入数据。



### 2.浏览器的重排重绘

浏览器渲染大致分为四个阶段，其中在解析 HTML 后，会依次进入 Layout 和 Paint 阶段。**样式或节点的更改，以及对布局信息的访问等**，都有可能导致重排和重绘。而重排和重绘的过程在**主线程**中进行，这意味着不合理的重排重绘会导致**渲染卡顿，用户交互滞后**等性能问题。



- 什么是重排重绘

  https://blog.usejournal.com/what-the-heck-is-repaint-and-reflow-in-the-browser-b2d0fb980c08

  ![image](https://user-images.githubusercontent.com/17002181/126033732-d5002255-1c88-4dee-9371-da166aacdca9.png)

  1. Parse + Style：相关引擎分别解析文档和样式表以及脚本，生成 DOM 和 CSSOM 。
  2. Layout： 将 DOM 与 CSSOM 合并成一个渲染树。浏览器通过 Render 树中的信息，以递归的形式计算出每个节点的尺寸大小和在页面中的具体位置。
  3. Paint：浏览器将 Render 树中的节点转换成在屏幕上绘制实际像素的指令，这个过程发生在多个图层上。
  4. Composite：浏览器将所有层按照一定顺序合并为一个图层并绘制在屏幕上。

  图中所示步骤为浏览器渲染的关键路径。浏览器从获取文档、样式、脚本等内容，到最终渲染结果到屏幕上，通常需要经过如图所示的步骤。而 DOM 或 CSSOM 被修改，会导致浏览器重复执行图中的步骤。重排和重绘，本质上指的就是触发 Layout 和 Paint 的过程，且重排必定导致重绘。

- 引起重排/重绘的操作

  - 修改元素的绘制属性，例如更改`color` `opacity`等会导致重绘。
  - 修改元素的几何属性，改变布局结构或者节点内容时，会导致重排。
  - 获取布局信息时，会导致重排。相关的方法属性如 `offsetTop` `getComputedStyle` 等。

- 如何减少重排/重绘

  - 对 DOM 进行批量写入和读取（通过虚拟 DOM 或者 `DocumentFragment` 实现）。
  - 避免对样式频繁操作，了解常用样式属性触发 Layout / Paint / Composite 的[机制](https://csstriggers.com/)，合理使用样式。
  - 使用 CSS Transforms 和 Animations：它可以让浏览器仅仅使用合成器来合成所有的层就可以达到动画效果，而不需要重新计算布局，重新绘制图形。[CSS Triggers](https://csstriggers.com/) 中仅触发 Composite 的属性就是最优的选择。（避开重排和重绘阶段，直接在非主线程上执行**合成**操作。）
  - 使用变量对布局信息（如 `clientTop`）进行缓存，避免因频繁读取布局信息而触发重排和重绘。



### 3.浏览器渲染机制

- **预加载扫描器**：主线程在解析DOM时并发运行一个线程。如果 HTML 中存在 `img` 或 `link` 之类的内容，则预加载扫描器会查看 HTML parser 生成的标记，并发送请求到浏览器进程的网络线程获取这些资源。

- script脚本阻塞DOM生成：当 HTML 解析器发现 script 标签时，会暂停 HTML 的解析，转而开始加载、解析和执行 JavaScript。因为 JS 可能会改变 DOM 的结构。如果不想因 JS 阻塞 HTML 的解析，可以为 script 标签添加 defer 属性或将 script 放在 body 结束标签之前，浏览器会在最后执行 JS 代码，避免阻塞 DOM 构建。

- 优化方法：

  在浏览器解析 HTML 的过程中，CSS 和 JS 都有可能对页面的渲染造成影响。优化方法包括以下几点：

  1. 关键 CSS 资源放在头部加载。
  2. JS 通常放在页面底部。
  3. 为 JS 添加 `async` 和 `defer` 属性。
  4. body 中尽量不要出现 CSS 和 JS。
  5. 为 `img` 指定宽高，避免图像加载完成后触发重排。
  6. 避免使用 `table`, `iframe` 等慢元素。原因是 table 会等到它的 Dom 树全部生成后再一次性插入页面中；`iframe` 内资源的下载过程会阻塞父页面静态资源的下载及 CSS, Dom 树的解析。

- ![script element](https://html.spec.whatwg.org/images/asyncdefer.svg)

### 4.垃圾回收机制

GC（Garbage Collection，垃圾回收）是一种内存自动管理机制， 垃圾回收器（Garbage Collector）可以自动回收分配给程序的已经不再使用的内存。常见的 GC 算法有引用计数法和标记清除法等。V8（JavaScript 引擎，提供执行 JavaScript 的运行时环境）的垃圾回收器算法主要由 Mark-Compact 和 Scavenger 构成。



- 内存泄漏：内存泄漏是指，应当被回收的对象没有被正常回收，变成常驻老生代的对象，导致内存占用越来越高。内存泄漏会导致应用程序速度变慢、高延时、崩溃等问题。
- 内存泄漏常见原因：
  - 创建全局变量，且没有手动回收。
  - 事件监听器 / 定时器 / 闭包等未正常清理。
  - 使用 JavaScript 对象来做缓存，且不设置过期策略和对象大小控制。

### 5.浏览器事件循环

浏览器需要事件循环来协调事件、用户操作、脚本执行、渲染、网络请求等。通过事件循环，浏览器可以利用**任务队列**来管理任务，让异步事件**非阻塞**地执行。每个客户端对应的事件循环是相对独立的。

###### 事件循环算法

1. 从 **宏任务** 队列（例如 “script”）中出队（dequeue）并执行最早的任务。
2. 执行所有微任务：
   - 当微任务队列非空时：
     - 出队（dequeue）并执行最早的微任务。
3. 如果有变更，则将变更渲染出来。
4. 如果宏任务队列为空，则休眠直到出现宏任务。
5. 转到步骤 1。

安排（schedule）一个新的 **宏任务**：

- 使用零延迟的 `setTimeout(f)`。

它可被用于将繁重的计算任务拆分成多个部分，以使浏览器能够对用户事件作出反应，并在任务的各部分之间显示任务进度。

此外，也被用于在事件处理程序中，将一个行为（action）安排（schedule）在事件被完全处理（冒泡完成）后。

安排一个新的 **微任务**：

- 使用 `queueMicrotask(f)`。
- promise 处理程序也会通过微任务队列。

###### 注意事项

1. when a task (in macrotask queue) is running, new events may be registered.So new tasks may be created.Below are two new created tasks:
   - promiseA.then()'s callback is a task
     - promiseA is resolved/rejected:  the task will be pushed into microtask queue in current round of event loop.
     - promiseA is pending:  the task will be pushed into microtask queue in the future round of event loop(may be next round)
   - setTimeout(callback,n)'s callback is a task,and will be pushed into macrotask queue,even n is 0;
2. task in microtask queue will be run in the current round,while task in macrotask queue has to wait for next round of event loop.
3. we all know callback of "click","scroll","ajax","setTimeout"... are tasks, however we should also remember js codes as a whole in script tag is a task(a macrotask) too.

**macrotasks:** [setTimeout](https://developer.mozilla.org/docs/Web/API/WindowTimers/setTimeout), [setInterval](https://developer.mozilla.org/docs/Web/API/WindowTimers/setInterval), [setImmediate](https://developer.mozilla.org/docs/Web/API/Window/setImmediate), [requestAnimationFrame](https://developer.mozilla.org/docs/Web/API/window/requestAnimationFrame), [I/O](https://developer.mozilla.org/docs/Mozilla/Projects/NSPR/Reference/I_O_Functions), UI rendering
**microtasks:** [process.nextTick](https://nodejs.org/uk/docs/guides/event-loop-timers-and-nexttick/), [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), [queueMicrotask](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/queueMicrotask), [MutationObserver](https://developer.mozilla.org/docs/Web/API/MutationObserver)



### 6.前端路由实现

- 路由：对于 Web 开发来说，路由的实质是 URL 到对应的处理程序的映射。
- 前端路由实现：可以分为 **Hash 路由** 和 **History 路由**。
- 前端路由对比服务器端路由：页面可以在无刷新的情况下进行页面的切换。

###### Hash路由

URI中的fragment部分是Hash路由读取的部分。

通过 `location.hash` 来获取到当前的 hash 路由。方法返回一个字符串，包含URL标识中的 `'#'` 和 后面URL片段标识符。如果URL中没有fragment，返回空字符串。

fragment 有以下特点：

- 修改 fragment 的内容不会触发网页重载。
- 修改 fragment 的内容会改变浏览器的历史记录。
- 修改 fragment 的内容会触发浏览器的 onhashchange 事件。

实现原理：监听 `hashchange` 事件来监听 url 中 hash 的变化，通过解析 hash 的值来切换页面。

优点：

1. 兼容性最佳。
2. 无需服务端配置。

缺点：

1. 服务端无法获取 hash 部分内容。
2. 可能和锚点功能冲突。
3. SEO 不友好。

###### History路由

`history API` 是 `H5` 提供的新特性，允许开发者**直接更改前端路由**，即更新浏览器 `URL` 地址而**不重新发起请求**。

实现原理：History 路由核心主要依赖 History API 里的两个方法和一个事件，其中两个方法用于操作浏览器的历史记录，事件用于监听历史记录的切换。

- 方法：
  - `history.pushState`：将给定的 Data 添加到当前标签页的历史记录栈中。
  - `history.replaceState`：将给定的 Data 更新到历史记录栈中最新的一条记录中。
  - `history.state`：用于存储以上方法的data数据，不同浏览器的读写权限不一样。
- 事件：
  - `popstate`：监听历史记录的变化。

### 7.浏览器缓存机制(HTTP)

###### 强制缓存

- `Expires`(HTTP/1.0) 
- `Cache-Control`(HTTP/1.1)(`max-age=?s`, `no-cache`, `no-store`,etc)

###### 协商缓存

- `Last-Modified` `If-Modified-Since`
- `Etag` `If-None-Match`

###### 缓存位置
- 从 Service Worker 中读取缓存（只支持 HTTPS）。
- 从内存读取缓存时 network 显示 memory cache。
- 从硬盘读取缓存时 network 显示 disk cache。
- Push Cache（推送缓存）（HTTP/2.0）。
- 优先级 Service Worker > memory cache > disk cache > Push Cache。

![img](https://cdn.nlark.com/yuque/0/2021/png/1500604/1618399660902-60a33dae-cedc-4bd0-9a5b-160c5da3f516.png)

###### F5与Ctrl+F5及地址栏输入地址回车的区别

- 地址栏输入地址然后回车: 根据缓存内容是否过期决定是否发送请求给服务端

- F5: 浏览器无论如何都得发送请求给服务端，包含If-Modified-Since/If-None_match，因此可能返回304

- Ctrl+F5:彻底从浏览器获取一份新的资源，请求中不会包含If-Modified-Since/If-None-Match，因此不可能返回304

![img](https://images2017.cnblogs.com/blog/1044429/201711/1044429-20171104170234451-1939856383.png)

###### `ETag` / `If-None-Match`

- 通过唯一标识来验证缓存。
- 优先级高于 `Last-Modified` / `If-Modified-Since`。

如果资源请求的响应头里含有 `ETag`，客户端可以在后续的请求的头中带上 `If-None-Match` 头来验证缓存。若服务器判断资源标识一致，则返回 304 状态码告知浏览器可从本地读取缓存。

唯一标识内容是由服务端生成算法决定的，可以是资源内容生成的哈希值，也可以是最后修改时间戳的哈希值。所以 `Etag` 标识改变并不代表资源文件改变，反之亦然。

###### `Last-Modified` / `If-Modified-Since`

- 通过资源的最后修改时间来验证缓存。
- 优先级低于 `ETag` / `If-None-Match`。
- 缺点：只能精确到秒，若 1s 内多次修改资源 `Last-Modified` 不会变化。

如果资源请求的响应头里含有 `Last-Modified`，客户端可以在后续的请求的头中带上 `If-Modified-Since` 头来验证缓存。若服务器判断资源最后修改时间一致，则返回 304 状态码告知浏览器可从本地读取缓存。

###### 缓存的优缺点

**优点**

- 节省了不必要的数据传输，节省带宽。
- 减少服务端的负担，提高网站性能。
- 降低网络延迟，加快页面响应速度，增强用户体验。

**缺点**

- 不恰当的缓存设置可能会导致资源更新不及时，导致用户获取信息滞后。

### 8. 如何监控网页崩溃

https://medium.com/@JackPu/how-to-check-browser-crash-via-javascript-fa7d5af5e80b

### 9. Cookie localStorage sessionStorage

https://scotch.io/@PratyushB/local-storage-vs-session-storage-vs-cookie

### 10. 什么是 Web Worker？ 什么是Service Worker？

https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers

https://developer.mozilla.org/zh-CN/docs/Web/API/Service_Worker_API

#### 11. 不同图片格式的区别，各有什么优缺点

主流格式：PNG JPG GIF WebP

新格式AVIF 

https://wpmudev.com/blog/best-image-formats-png-vs-jpg-svg-gif-webp/

#### 12. 如何实现图片懒加载？

原生：<img loading=lazy> defers offscreen images until the user scrolls near them.

其他方法的实质是什么

[intersection observer api](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API) ？

#### 13. 什么是进程，什么是线程

https://stackoverflow.com/questions/200469/what-is-the-difference-between-a-process-and-a-thread/19518207#19518207

#### 14. 客户端路由是怎么工作的

有三种路由，BrowserRouter, HashRouter, MemoryRouter，它们的区别是什么，是如何工作的。



To best understand this, you are suggested to write your own client-side router, with

1. [pushState](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState) and [popstate event](https://developer.mozilla.org/en-US/docs/Web/API/Window/popstate_event)
2. [onhashchange](https://developer.mozilla.org/en-US/docs/Web/API/WindowEventHandlers/onhashchange)

#### 15. script 标签中 async 和 defer 属性的作用和区别

（1）脚本没有 defer 或 async，浏览器会立即加载并执行指定的脚本，也就是说不等待后续载入的文档元素，读到就加载并执行。

（2）defer 属性表示延迟执行引入的 JavaScript，即这段 JavaScript 加载时 HTML 并未停止解析，这两个过程是并行的。当整个 document 解析完毕后再执行脚本文件，在 DOMContentLoaded 事件触发之前完成。多个脚本按顺序执行。

（3）async 属性表示异步执行引入的 JavaScript，与 defer 的区别在于，如果已经加载好，就会开始执行，也就是说它的执行仍然会阻塞文档的解析，只是它的加载过程不会阻塞。多个脚本的执行顺序无法保证。

