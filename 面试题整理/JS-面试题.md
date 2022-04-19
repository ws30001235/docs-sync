## JS-面试题

#### 1. 什么是闭包

闭包是由函数以及声明该函数的词法环境组合而成的。该环境包含了这个闭包创建时作用域内的任何局部变量。

所有的函数在“诞生”时都会记住创建它们的词法环境。从技术上讲，这里没有什么魔法：所有函数都有名为 `[[Environment]]` 的隐藏属性，该属性保存了对创建该函数的词法环境的引用。

闭包让你可以在一个内层函数中访问到其外层函数的作用域。在 JavaScript 中，每当创建一个函数，闭包就会在函数创建的同时被创建出来。



###### 什么是词法环境？

在 JavaScript 中，每个运行的函数，代码块 `{...}` 以及整个脚本，都有一个被称为 **词法环境（Lexical Environment）** 的内部（隐藏）的关联对象。

词法环境对象由两部分组成：

1. **环境记录（Environment Record）** —— 一个存储所有局部变量作为其属性（包括一些其他信息，例如 `this` 的值）的对象。
2. 对 **外部词法环境** 的引用，与外部代码相关联。

一个“变量”只是 **环境记录** 这个特殊的内部对象的一个属性。“获取或修改变量”意味着“获取或修改词法环境的一个属性”。



#### 2. Promise

传递给 `new Promise` 的函数被称为 **executor**。当 `new Promise` 被创建，executor 会自动运行。它包含最终应产出结果的生产者代码。

它的参数 `resolve` 和 `reject` 是由 JavaScript 自身提供的回调。我们的代码仅在 executor 的内部。

当 executor 获得了结果，无论是早还是晚都没关系，它应该调用以下回调之一：

- `resolve(value)` —— 如果任务成功完成并带有结果 `value`。
- `reject(error)` —— 如果出现了 error，`error` 即为 error 对象。

总结一下就是：executor 会自动运行并尝试执行一项工作。尝试结束后，如果成功则调用 `resolve`，如果出现 error 则调用 `reject`。

由 `new Promise` 构造器返回的 `promise` 对象具有以下内部属性：

- `state` —— 最初是 `"pending"`，然后在 `resolve` 被调用时变为 `"fulfilled"`，或者在 `reject` 被调用时变为 `"rejected"`。
- `result` —— 最初是 `undefined`，然后在 `resolve(value)` 被调用时变为 `value`，或者在 `reject(error)` 被调用时变为 `error`。





`then()`：返回一个 [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。它最多需要有两个参数：Promise 的成功和失败情况的回调函数。

- `onFulfilled` 可选

  当 Promise 变成接受状态（fulfilled）时调用的[`函数`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function)。该函数有一个参数，即接受的最终结果（the fulfillment  value）。如果该参数不是函数，则会在内部被替换为 `(x) => x`，即原样返回 promise 最终结果的函数

- `onRejected` 可选

  当 Promise 变成拒绝状态（rejected）时调用的[`函数`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function)。该函数有一个参数，即拒绝的原因（`rejection reason`）。 如果该参数不是函数，则会在内部被替换为一个 "Thrower" 函数 (it throws an error it received as argument)。

- handler返回值 => then()返回值

  - 值。返回的promise的状态为fulfilled, 结果为这个值。
  - 没有return。返回的promise的状态为fulfilled, 结果为undefined。
  - 抛出错误。返回的promise状态为rejected，结果为抛出的错误。
  - 返回一个fulfilled或rejected状态的promise，then()返回的promise的以这个promise的值为值fulfilled。
  - returns another **pending** promise object, the resolution/rejection of the promise returned by `then` will be subsequent to the resolution/rejection of the promise returned by the handler. Also, the resolved value of the promise returned by `then` will be the same as the resolved value of the promise returned by the handler.

`catch(f)` 实质是和`.then(null, f)`一样的。只是一个简写形式。

`finally()` 是执行清理（cleanup）的很好的处理程序（handler），例如无论结果如何，都停止使用不再需要的加载指示符（indicator）。

1. `finally` 处理程序（handler）没有参数。在 `finally` 中，我们不知道 promise 是否成功。没关系，因为我们的任务通常是执行“常规”的定稿程序（finalizing procedures）。
2. `finally` 处理程序将之前的结果和 error 传递给下一个处理程序。

#### 3. 什么是原型继承，有什么用

JS中每个对象都有一个私有属性[[prototype]]，通过[[prototype]]引用的对象是原型。通过调用构造函数生成的对象的[[prototype]]指向构造函数的prototype属性，也称为原型对象。如果我们想要读取对象的一个属性或调用一个方法，并且它不存在于当前对象中，那么JS就会尝试在原型对象中查找。然后，在原型对象的原型对象中查找，直到找到匹配的属性或方法或者当前查找对象的原型对象是null，null没有原型对象，到达原型链的末尾。

通过原型继承，可以实现方法的复用，所有对象实例可以共享原型对象的方法和属性，减少内存的消耗。比如JS内建对象如Array, Object，方法都存储在prototype中，对象本身只存储数据。

#### 4. 解释JS中的`this`关键字

`this`是当前执行上下文（global、function 或 eval）的一个属性。

在全局执行环境中，`this`指向全局对象。

在函数内部，this的值取决于函数被调用的方式。在严格模式下，如果进入执行环境时没有设置 `this` 的值，`this` 会保持为 `undefined`。非严格模式下，`this`指向全局对象。

用 `call` 或者`apply` 方法调用函数时，`this`指向第一个参数。

调用`f.bind(someObject)`会创建一个与`f`具有相同函数体和作用域的函数，但是在这个新函数中，`this`将永久地被绑定到了`bind`的第一个参数，无论这个函数是如何被调用的。

在[箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)中，`this`与封闭词法环境的`this`保持一致。在全局代码中，它将被设置为全局对象。否则它会继承上一级不是箭头函数的函数中的this。

当函数作为对象里的方法被调用时，`this` 被设置为调用该函数的对象。

#### 5. var, let, const的区别

使用 let const 声明的变量的作用域是块级作用域。而var声明的变量的作用域是它当前的执行上下文，对于声明在任何函数外的变量，它的作用域是全局作用域，而且是全局对象的属性。

使用var进行的变量声明会被提升，实质是在编译阶段，变量声明被添加到词法环境中且值被初始化为undefined。使用let const声明的变量存在暂时性死区，在变量声明之前访问会抛出引用错误。

#### 6. 什么是事件委托

https://javascript.info/event-delegation

#### 7. 解释 AMD, CommonJS 和 ES Modules 的不同

https://auth0.com/blog/javascript-module-systems-showdown/

#### 8. `instanceof` 的原理是什么？和 `typeof`的区别是什么？

```javascript
object instanceof constructor
```

`instanceof `操作符判断构造函数的`prototype`属性是否出现在该实例对象的原型链中

`typeof` 操作符可以判断原始值的类型，以及function对象和其他对象，`null`得到的结果是`object`