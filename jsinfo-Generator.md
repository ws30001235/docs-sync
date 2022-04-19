## Generator

`function*` generator function

在此类函数被调用时，它不会运行其代码。而是返回一个被称为 “generator object” 的特殊对象，来管理执行流程。

一个 generator 的主要方法就是 `next()`。执行直到最近的 `yield <value>` 语句，然后函数执行暂停，并将产出的（yielded）值返回到外部代码。

`next()` 的结果始终是一个具有两个属性的对象：

- `value`: 产出的（yielded）的值。
- `done`: 如果 generator 函数已执行完成则为 `true`，否则为 `false`。

generator 是 [可迭代（iterable）](https://zh.javascript.info/iterable)的。我们可以使用 `for..of` 循环遍历它所有的**值**。当 `done: true` 时，`for..of` 循环会忽略最后一个 `value`。因此，如果我们想要通过 `for..of` 循环显示所有的结果，我们必须使用 `yield` 返回它们。(而不是return最后一个值)

Generator 组合（composition）：对于 generator 而言，我们可以使用 `yield*` 这个特殊的语法来将一个 generator “嵌入”（组合）到另一个 generator 中。`yield*` 指令将执行 **委托** 给另一个 generator。