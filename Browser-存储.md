### Cookie

###### 读取与写入

+ `document.cookie` 的值由 `name=value` 对组成，以 `;` 分隔。每一个都是独立的 cookie。
+ 我们可以使用 `document.cookie` 属性从浏览器访问 cookie。这是一个访问器属性。
+ Cookie 通常是由 Web 服务器使用响应 `Set-Cookie` HTTP-header 设置的。然后浏览器使用 `Cookie` HTTP-header 将它们自动添加到（几乎）每个对相同域的请求中。
+ `document.cookie` 的值由 `name=value` 对组成，以 `;` 分隔。每一个都是独立的 cookie。
+ 一个 cookie 最大为 4KB。每个域的 cookie 总数不得超过 20+ 左右，具体限制取决于浏览器。
+ 使用`encodeURIComponent`函数对cookie的名称和值进行转义。
+ 写入操作只会修改其中提到的 cookie。
+ cookie的选项列在 `key=value` 之后，以 `;` 分隔。e.g `document.cookie = "user=John; path=/; expires=Tue, 19 Jan 2038 03:14:07 GMT"`

###### Cookie的选项

- `path=/mypath`: url 路径前缀。该路径下的页面可以访问该 cookie。必须是绝对路径。默认为当前路径。通常，我们应该将 `path` 设置为根目录：`path=/`，以使 cookie 对此网站的所有页面可见。
- `domain=site.com`: 默认 cookie 仅在当前域下可见，如果显式设置了域，可以使 cookie 在子域下也可见。
- `expires`, `max-age`
  - 如果一个 cookie 没有设置这两个参数中的任何一个，那么在关闭浏览器之后，它就会消失。此类 cookie 被称为 "session cookie”。
  - `expires=Tue, 19 Jan 2038 03:14:07 GMT`cookie 的到期日期，那时浏览器会自动删除它。
    - 使用 `date.toUTCString` 来获取GMT时区的日期格式
  - `max-age=3600`指明 cookie 的过期时间距离当前时间的秒数。
- `secure`: **默认情况下，如果我们在 `http://site.com` 上设置了 cookie，那么该 cookie 也会出现在 `https://site.com` 上，反之亦然。**使用此选项，如果一个 cookie 是通过 `https://site.com` 设置的，那么它不会在相同域的 HTTP 环境下出现，例如 `http://site.com`。
- `samesite`: 用于防止XSRF攻击。如果请求来自外部网站，禁止浏览器发送 cookie。
  - `samesite=strict`（和没有值的 `samesite` 一样)：用户进行任何来自其他域下的操作，cookie 都不会被发送。
  - `samesite=lax`: 宽松（lax）模式，和 `strict` 模式类似，当从外部来到网站，则禁止浏览器发送 cookie，但是增加了一个例外。满足以下两个条件时，会发送cookie。
    - HTTP 方法是“安全的”（例如 GET 方法，而不是 POST）。
    - 该操作执行顶级导航（更改浏览器地址栏中的 URL）。
- `httpOnly`: 这个选项禁止任何 JavaScript 访问 cookie。我们使用 `document.cookie` 看不到此类 cookie，也无法对此类 cookie 进行操作。

###### 第三方cookie

如果 cookie 是由用户所访问的页面的域以外的域放置的，则称其为第三方 cookie。

第三方 cookie 通常用于跟踪和广告服务。它们被绑定在原始域上，通过它可以在不同网站之间跟踪同一用户。

一些现代浏览器对此类 cookie 采取特殊策略：

- Safari 浏览器完全不允许第三方 cookie。
- Firefox 浏览器附带了一个第三方域的黑名单，它阻止了来自名单内的域的第三方 cookie。

---

### localStorage, sessionStorage

Web 存储对象 `localStorage` 和 `sessionStorage` 允许我们在浏览器上保存键/值对。

###### 与cookie的不同之处

- Web 存储对象不会随每个请求被发送到服务器。因此，我们可以保存更多数据（存储大小限制为 5MB+，具体取决于浏览器）。
- 服务器无法通过 HTTP header 操纵存储对象。一切都是在 JavaScript 中完成的。
- 存储绑定到源（域/协议/端口三者）。也就是说，不同协议或子域对应不同的存储对象，它们之间无法访问彼此数据。

###### localStorage 与 sessionStorage 的方法和属性（API）

- `setItem(key, value)` —— 存储键/值对。
- `getItem(key)` —— 按照键获取值。
- `removeItem(key)` —— 删除键及其对应的值。
- `clear()` —— 删除所有数据。
- `key(index)` —— 获取该索引下的键名。
- `length` —— 存储的内容的长度。

- 使用 `Object.keys` 来获取所有的键。
- 我们将键作为对象属性来访问，在这种情况下，不会触发 `storage` 事件。

###### 共同的特点

- 可以用类对象形式访问，但是不建议。
- 键和值都必须是字符串。如果是任何其他类型，例数字或对象，它会被自动转换为字符串。

###### localStorage

- 特点：
  - 在同源的所有标签页和窗口之间共享数据。
  - 数据不会过期。它在浏览器重启甚至系统重启后仍然存在。

###### sessionStorage

- 特点：
  - `sessionStorage` 的数据只存在于当前浏览器标签页（以及同源的iframe）。具有相同页面的另一个标签页中将会有不同的存储。
  - 数据在页面刷新后仍然保留，但在关闭/重新打开浏览器标签页后不会被保留。
- 因为 `sessionStorage` 不仅绑定到源，还绑定在同一浏览器标签页。因此，`sessionStorage` 很少被使用。

###### Storage事件

当 `localStorage` 或 `sessionStorage` 中的数据更新后，`storage`事件会在所有可访问到存储对象的 `window` 对象上触发，导致当前数据改变的 `window` 对象除外。

`storage`事件的属性：

- `key` —— 发生更改的数据的 `key`（如果调用的是 `.clear()` 方法，则为 `null`）。
- `oldValue` —— 旧值（如果是新增数据，则为 `null`）。
- `newValue` —— 新值（如果是删除数据，则为 `null`）。
- `url` —— 发生数据更新的文档的 url。
- `storageArea` —— 发生数据更新的 `localStorage` 或 `sessionStorage` 对象。

---

