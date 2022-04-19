# JS中判断数据类型的方法

### 1.`typeof`运算符

- 可以判断`undefined`,`Boolean`,`Number`,`String`,`Symbol`
- 除了`function`,其他所有引用类型的结果为`object`
- `typeof null == 'object'`

### 2.`instanceof`运算符

使用 `obj instanceOf Class` 检查 `Class.prototype` 是否等于 `obj` 的原型链中的原型之一。

### 3.`constructor`属性

对象继承了原型的`constructor`，而该属性默认是指向构造函数的

### 4.Object.prototype.toString()方法

方法借用: `Object.prototype.toString.call(obj)`

### 5.Array.isArray()方法

判断是否为数组就用这个