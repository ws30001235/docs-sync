## Fullstack Open

useEffect会在组件渲染完成之后立即执行。

通常，对状态更新函数的调用会触发组件的重新渲染。

`export default { getAll, create, update } `实际导出的是一个对象。（由于键和赋值变量的名称是相同的，我们可以用更简洁的语法来编写对象定义）



React中的内联样式不能直接使用伪类。



我们可以使用冒号语法为express路由定义[参数](http://expressjs.com/en/guide/routing.html#route-parameters) 。eg：`app.get('/api/notes/:id', ...)`

请求路由中的*id* 参数可以通过[request](http://expressjs.com/en/api.html#req)对象访问: `const id = request.params.id`



`response.status(404).end()` 由于响应没有附加任何数据，我们使用[status](http://expressjs.com/en/4x/api.html#res.status)方法来设置状态，并使用[end](http://expressjs.com/en/4x/api.html#res.end)方法来响应request而不发送任何数据。



为了方便地访问数据，我们需要 express [json-parser](https://expressjs.com/en/api.html)的帮助，它与命令`app.use(express.json())`一起使用。如果没有 json-parser，*request.body* 属性将是undefined的。Json-parser 的功能是获取请求的 JSON 数据，将其转换为 JavaScript 对象，然后在调用路由处理程序之前将其附加到请求对象的 *body* 属性。



让我们实现我们自己的中间件，打印有关发送到服务器的每个请求的信息。

中间件是一个接收三个参数的函数:

```js
const requestLogger = (request, response, next) => {
  console.log('Method:', request.method)
  console.log('Path:  ', request.path)
  console.log('Body:  ', request.body)
  console.log('---')
  next()
}
```

在函数体的末尾，调用作为参数传递的下一个函数。 函数将控制权交给下一个中间件。

中间件是这样使用的:

```js
app.use(requestLogger)
```





还有一些情况，我们希望在路由之后定义中间件函数。 实际上，这意味着我们定义的中间件函数只有在没有路由处理 HTTP 请求的情况下才被调用。



当我们在中间件声明中添加如下内容时

```js
app.use(express.static('build'))
```

每当 express 收到一个 HTTP GET 请求时，它都会首先检查*build* 目录是否包含与请求地址对应的文件。 如果找到正确的文件，express 将返回该文件。