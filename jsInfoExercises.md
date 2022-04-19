### 5.8 弱映射和弱集合

+ 知识点：
  
  + `WeakMap` 的键必须是对象，不能是原始值
  
  + 如果我们在 weakMap 中使用一个对象作为键，并且没有其他对这个对象的引用：该对象将会被从内存（和map）中自动清除。
  
  + 只能向 `WeakSet` 添加对象（而不能是原始值）。
  
  + 对象只有在其它某个（些）地方能被访问的时候，才能留在 set 中。
  
  + `WeakMap` 和 `WeakSet` 最明显的局限性就是不能迭代，并且无法获取所有当前内容。

+ 习题
  
  1. 将messages数组中的对象存储到一个弱集合中，取名为readMessages；
     
     另一个方法是用symbol创建一个属性避免冲突，该属性标识了是否已读
     
     ```javascript
     // symbol 属性仅对于我们的代码是已知的
     
     let isRead = Symbol("isRead"); 
     messages[0][isRead] = true; `
     ```
     
     尽管 symbol 可以降低出现问题的可能性，但从架构的角度来看，还是使用 `WeakSet` 更好。
  
  2. 创建一个WeakMap，键为messages数组中的消息对象，值为加入该弱集合时创建的Date对象

---

### 5.9 Object.keys，values，entries

+ 知识点：
  
  + 转换对象，可以使用 `Object.entries`，应用数组方法，然后使用 `Object.fromEntries`转换回来
  + 注意对象方法与Map的方法的区别
    + | Map | Object |
      | 调用语法 | `map.keys()` | `Object.keys(obj)`，而不是 `obj.keys()` |
      | 返回值 | 可迭代项 | “真正的”数组 |

+ 习题
  
  1. ```javascript
     function sumSalaries(salaries) {
         let sum = 0;
         for (let salary of Object.values(salaries)) {
             sum += salary;
         }
         return sum;
     }
     ```
  
  2. ```javascript
     function count(obj) {
         return Object.keys(obj).length;
     }
     ```

### 5.10 解构赋值

+ 知识点
  
  + 解构对象的完整语法：
    
    `let {prop : varName = default, ...rest} = object`
    
    这表示属性 `prop` 会被赋值给变量 `varName`，如果没有这个属性的话，就会使用默认值 `default`。
    
    没有对应映射的对象属性会被复制到 `rest` 对象。
  
  + 解构数组的完整语法：
    
    `let [item1 = default, item2, ...rest] = array`
    
    数组的第一个元素被赋值给 `item1`，第二个元素被赋值给 `item2`，剩下的所有元素被复制到另一个数组 `rest`。

+ 习题
  
  1. ```javascript
     const {name, years: age, isAdmin = false} = user;
     ```
  
  2. ```javascript
     function topSalary(salaries) {
         let top = -Infinity;
         let resName = null;
         for (let [name, salary] of Object.entries(salaries)) {
             if (salary > top) {
             resName = name;
             top = salary;
             }
         }
         return resName;
     }
     ```

### 5.11 日期和时间

+ 知识点
  
  + 月份从 0 开始计数（对，一月是 0）。
    
    一周中的某一天 `getDay()` 同样从 0 开始计算（0 代表星期日）。
    
    当设置了超出范围的组件时，`Date` 会进行自我校准。这一点对于日/月/小时的加减很有用。
    
    日期可以相减，得到的是以毫秒表示的两者的差值。因为当 `Date` 被转换为数字时，`Date` 对象会被转换为时间戳。
    
    JavaScript 自身并没有测量微秒的方法（百万分之一秒），但大多数运行环境会提供。浏览器有 [performance.now()](https://developer.mozilla.org/zh/docs/Web/API/Performance/now) 方法，Node.js 有 `microtime` 模块以及其他方法。

+ 习题
  
  <mark>**第4题** </mark> <mark>第6题</mark> 第8题
  
  1. `let date = new Date(2012, 1, 20, 3, 12);`
  
  2. ```javascript
     function getWeekDay(date) {
       let days = ['SU', 'MO', 'TU', 'WE', 'TH', 'FR', 'SA'];
     
       return days[date.getDay()];
     }
     ```
  
  3. ```javascript
     function getLocalDay(date) {
         let day = date.getDay();
         if (day == 0) return 7;
         return day;
     }
     ```
  
  4. ```javascript
     function getDateAgo(date, days) {
         let copy = new Date(date);
         copy.setDate(date.getDate() - days)
         return copy.getDate();
     }
     ```
  
  5. ```javascript
     function getLastDayOfMonth(year, month) {
         let date = new Date(year, month + 1, 0);
         return date.getDate();
     }
     ```
  
  6. ```javascript
     function getSecondsToday() {
         let now = new Date();
         let today = newDate(now.getFullYear(), now.getMonth(), now.getDate());
         let diff = now - today;
         return Math.round(diff / 1000);
     }
     ```
  
  7. ```javascript
     function getSecondsToTomorrow() {
         let now = new Date();
         let tomorrow = new Date(now.getFullYear(), now.getMonth(), now.getDate()+1)
         let diff = tomorrow - now;
         return Math.round(diff / 1000);
     }
     ```
  
  8. ```javascript
     function formatDate(date) {
       let diff = new Date() - date; // 以毫秒表示的差值
     
       if (diff < 1000) { // 少于 1 秒
         return 'right now';
       }
     
       let sec = Math.floor(diff / 1000); // 将 diff 转换为秒
     
       if (sec < 60) {
         return sec + ' sec. ago';
       }
     
       let min = Math.floor(diff / 60000); // 将 diff 转换为分钟
       if (min < 60) {
         return min + ' min. ago';
       }
     
       // 格式化 date
       // 将前置 0 加到一位数 day/month/hours/minutes 前
       let d = date;
       d = [
         '0' + d.getDate(),
         '0' + (d.getMonth() + 1),
         '' + d.getFullYear(),
         '0' + d.getHours(),
         '0' + d.getMinutes()
       ].map(component => component.slice(-2)); // 得到每个组件的后两位
     
       // 将时间信息和日期组合在一起
      return d.slice(0, 3).join('.') + ' ' + d.slice(3).join(':');
     }
     ```

---

### 5.12 JSON方法，toJSON

+ 知识点 
  
  + 如果一个对象具有 `toJSON`，那么它会被 `JSON.stringify` 调用。
  
  + JavaScript 提供序列化（serialize）成 JSON 的方法 [JSON.stringify](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) 和解析 JSON 的方法 [JSON.parse](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)。

+ 练习
  
  1. ```javascript
     let jsonData = JSON.stringify(user);
     let data = JSON.parse(jsonData);
     ```
  
  2. ```javascript
     
     ```

---

### 6.3 变量作用域，闭包

<mark>`for` 循环在每次迭代中，都会生成一个带有自己的变量 `i` 的新词法环境。</mark>

1. 闭包sum

```javascript
function func(a) {
    return function (b) {
        return a + b;
        }
}
```

2. 通过函数筛选

```javascript
function inBetween(a, b) {
    return function (x) {
        return x >= a && x <= b;
}
}
```

```javascript
function inArray(array) {
    return function (x) {
    return array.includes(x);
}
}
```

3. 按字段排序
   
   ```javascript
   function byField(field) {
       return function(a, b) {
           return a[field] > b[field] ? 1 : -1;
   }
   }
   ```
   
   ---
   
   使用 `new Function` 创建的函数，它的 `[[Environment]]` 指向全局词法环境，而不是函数所在的外部词法环境。因此，我们不能在 `new Function` 中直接使用外部变量。不过这样是好事，这有助于降低我们代码出错的可能。并且，从代码架构上讲，显式地使用参数传值是一种更好的方法，并且避免了与使用压缩程序而产生冲突的问题。
   
   语法：
   
   `let func = new Function ([arg1, arg2, ...argN], functionBody);`

---

### 6.9 装饰器模式和转发

1. 间谍装饰器
   
   ```javascript
   function spy(func) {
       function wrapper(...args) {
           wrapper.calls.push(args);
           return func.apply(this, args);
       }
       wrapper.calls = [];
   
       return wrapper;
   }
   ```

2. 延时装饰器
   
   ```javascript
   function delay(f, ms) {
      function wrapper() {
          setTimeout(() => f.apply(this, arguments), ms)
      }
       return wrapper;
   }
   ```

3. 防抖装饰器
   
   ```javascript
   function debounce(f, ms) {
       let timeout
       return function() {
           clearTimeout(timeout);
           timeout = setTimeout(() => func.apply(this, arguments), ms);
       }
   }
   ```

4. 节流装饰器
   
   ```javascript
   function throttle(f, ms) {
       let t1 = 0;
       return function() {
           let t2 = new Date();
           if (t2 - t1 > ms) {
               f.apply(this, arguments);
               t1 = t2;
           }  
       }
   }
   ```

---

### 6.10 函数绑定

+ 知识点：
  
  + 浏览器中的 `setTimeout` 方法有些特殊：它为函数调用设定了 `this=window`（对于 Node.js，`this` 则会变为计时器（timer）对象，但在这儿并不重要）。
  
  + `func.bind(context)` 的结果是一个特殊的类似于函数的“外来对象（exotic object）”，它可以像函数一样被调用，并且透明地（transparently）将调用传递给 `func` 并设定 `this=context`。
  
  + 偏函数：通过绑定先有函数的一些参数来创建一个新函数。
    
    + `let bound = func.bind(context, [arg1], [arg2], ...);`
    
    + 不需要context时可以设置为null
  
  + partial包装器函数

+ 练习
  
  1. 作为方法的绑定函数
     
     `null`
  
  2. <mark>**二次bind**</mark>
     
     `f.bind(...)` 返回的外来（exotic）绑定函数 对象仅在创建的时候记忆上下文（以及参数，如果提供了的话）。
     
     一个函数不能被重绑定（re-bound）。
  
  3. bind后的函数属性
     
     `undefined`。
     
     `bind` 的结果是另一个对象。原先函数中的属性会丢失。
  
  4. 修复丢失了this的函数
     
     bind上下文/ 箭头函数
  
  5. 偏函数在登录中的应用
     
     askPassword(() => user.login(true), () => user.login(false));

----

### 6.11 深入理解箭头函数

+ 知识点： 箭头函数：
  
  - 没有 `this`
  - 没有 `arguments`
    + 所以箭头函数中出现`this`和`arguments`都是从外部词法环境中获取的
  - 不能使用 `new` 进行调用
  - 它们也没有 `super`，但目前我们还没有学到它。我们将在 [类继承](https://zh.javascript.info/class-inheritance) 一章中学习它。

---

### 7.1 属性标志和属性描述符

+ 属性描述符对象：包含值和所有的标志
  
  `let descriptor = Object.getOwnPropertyDescriptor(obj, propertyName);`

+ 标志：
  
  - **`writable`** — 如果为 `true`，则值可以被修改，否则它是只可读的。
  - **`enumerable`** — 如果为 `true`，则会被在循环中列出，否则不会被列出。
  - **`configurable`** 

+ 修改标志：
  
  （属性不存在时使用给定的值和标志创建属性，没有提供的标志默认为`false`）
  
  `Object.defineProperty(obj, propertyName, descriptor)`

+ 克隆对象
  
  `let clone = Object.defineProperties({}, Object.getOwnPropertyDescriptors(obj));`

### 7.2 访问器属性

+ 访问器属性由 “getter” 和 “setter” 方法表示。在对象字面量中，它们用 `get` 和 `set` 表示

+ 访问器属性的描述符与数据属性的不同。
  
  对于访问器属性，没有 `value` 和 `writable`，但是有 `get` 和 `set` 函数。

----

### 8.1 原型继承

+ 知识点
  
  + 对象有一个特殊的隐藏属性 `[[Prototype]]`（如规范中所命名的），它要么为 `null`，要么就是对另一个对象的引用。
  
  + 当我们从 `object` 中读取一个缺失的属性时，JavaScript 会自动从原型中获取该属性。在编程中，这被称为“原型继承”。
  
  + 设置内部属性[[Prototype]] : 
    
    + 使用`__proto__` 属性 （值可以是对象，也可以是 `null`。而其他的类型都会被忽略。）`__proto__` 是 `[[Prototype]]` 的 getter/setter。（历史遗留）
    
    + `Object.getPrototypeOf`/`Object.setPrototypeOf`
  
  + `for..in` 循环也会迭代继承的属性。(不可枚举属性不会出现)。而`Object.keys()`只返回自己的key。`obj.hasOwnProperty(key)`判断key是否为非继承属性。
  
  + `for..in` 循环在其自身和继承的属性上进行迭代。所有其他的键/值获取方法仅对对象本身起作用。
  
  + 对象字面量默认继承于Object.prototype

+ 练习题
  
  1. true null undefined
  
  2. <u>在现代引擎中，从性能的角度来看，我们是从对象还是从原型链获取属性都是没区别的。它们（引擎）会记住在哪里找到的该属性，并在下一次请求中重用它。</u>
  
  3. rabbit

----

### 8.2 F.prototype

+ 知识点：
  
  + 如果 `F.prototype` 是一个对象，那么 `new` 操作符会使用它为`new F()`创建的新对象设置 `[[Prototype]]`。
  
  + `F.prototype` 属性仅在 `new F` 被调用时使用，它为新对象的 `[[Prototype]]` 赋值。
  
  + 每个函数都有 `"prototype"` 属性，即使我们没有提供它。默认的 `"prototype"` 是一个只有属性 `constructor` 的对象，属性 `constructor` 指向函数自身。
    
    + `Rabbit.prototype.constructor == Rabbit`
    
    + `let rabbit = new Rabbit();` `rabbit.constructor == Rabbit`
  
  + `prototype`属性指向的对象可以被修改和覆盖，因此如果要确保是正确的`constructor`。 添加/删除属性到默认 `"prototype"`，而不是将其整个覆盖。

+ 练习：
  
  1. （1）true  (2) false  (3)  true (4) undefined
  
  2. <mark>值得复习的题目</mark> 以下摘自部分答案
     
     例如：
     
     ```javascript
     function User(name) {
      this.name = name;
     }
     User.prototype = {}; // (*)
     
     let user = new User('John');
     let user2 = new user.constructor('Pete');
     
     alert( user2.name ); // undefined
     ```
     
     为什么 `user2.name` 是 `undefined`？
     
     这是 `new user.constructor('Pete')` 的工作流程：
     
     1. 首先，它在 `user` 中寻找 `constructor`。没找到。
     2. 然后它追溯原型链。`user` 的原型是 `User.prototype`，它也没有 `constructor`（因为我们“忘记”在右侧设定它了）。
     3. 再向上追溯，`User.prototype` 是一个普通对象 `{}`，其原型是 `Object.prototype`。
     4. 最终，对于内建的 `Object.prototype`，有一个内建的 `Object.prototype.constructor == Object`。所以就用它了。
     
     所以，最终我们得到了 `let user2 = new Object('Pete')`。
     
     可能这不是我们想要的。我们想创建 `new User` 而不是 `new Object`。这就是缺少 `constructor` 的结果。
     
     （以防你好奇，`new Object(...)` 调用会将其参数转换为对象。这是理论上的，在实际中没有人会调用 `new Object` 并传入一个值，通常我们也不会使用 `new Object` 来创建对象）

----

### 8.3 原生的原型

+ 知识点：
  
  + 如果试图访问字符串、数字和布尔值的属性，临时包装器对象将会通过内建的构造器 `String`、`Number` 和 `Boolean` 被创建。它们提供给我们操作字符串、数字和布尔值的方法然后消失。
  
  + 值 `null` 和 `undefined` 没有对象包装器。所以它们没有方法和属性。并且它们也没有相应的原型。
  
  + 原生的原型是可以被修改的。原型是全局的，所以很容易造成冲突。如果有两个库都添加了 `String.prototype.show` 方法，那么其中的一个方法将被另一个覆盖。所以，通常来说，修改原生原型被认为是一个很不好的想法。
  
  + **在现代编程中，只有一种情况下允许修改原生原型。那就是 polyfilling。**
    
    Polyfilling 是一个术语，表示某个方法在 JavaScript 规范中已存在，但是特定的 JavaScript 引擎尚不支持该方法，那么我们可以通过手动实现它，并用以填充内建原型。
  
  + 所有的内建对象都遵循相同的模式（pattern）：
    
    - 方法都存储在 prototype 中（`Array.prototype`、`Object.prototype`、`Date.prototype` 等）。
    - 对象本身只存储数据（数组元素、对象属性、日期）。
  
  + `obj.join = Array.prototype.join;` eg 借用原型中的方法

+ 习题：
  
  1. `Function.prototype.defer = function(ms) { setTimeout(this, ms); };`
  
  2. <mark>有必要复习</mark>
     
     ```javascript
     Function.prototype.defer = function(ms) {
     let f = this;
     return function(...args) {
       setTimeout(() => f.apply(this, args), ms);
     }
     };
     ```

----

### 8.4 原型方法，没有 `__proto__ `的对象

+ 知识点：
  
  - [Object.create(proto, [descriptors])](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/Object/create) —— 利用给定的 `proto` 作为 `[[Prototype]]` 和可选的属性描述来创建一个空对象。
  - [Object.getPrototypeOf(obj)](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf) —— 返回对象 `obj` 的 `[[Prototype]]`。
  - [Object.setPrototypeOf(obj, proto)](https://developer.mozilla.org/zh/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf) —— 将对象 `obj` 的 `[[Prototype]]` 设置为 `proto`。
  - 我们可以使用 `Object.create` 来实现比复制 `for..in` 循环中的属性更强大的对象克隆方式：
    
    `let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));`
    
    此调用可以对 `obj` 进行真正准确地拷贝，包括所有的属性：可枚举和不可枚举的，数据属性和 setters/getters —— 包括所有内容，并带有正确的 `[[Prototype]]`。
  - `Object.create(null)` 创建了一个空对象，这个对象没有原型（`[[Prototype]]` 是 `null`）

+ 练习题：
  
  1. 添加`dictionary.toString` <mark>不明白`value() {}` 这样的声明方式</mark>
     
     ```javascript
     let dictionary = Object.create(null, {
       toString: { // 定义 toString 属性
         value() { // value 是一个 function
           return Object.keys(this).join();
         }
       }
     });
     ```

---

### 9.1 Class基本语法 9.2 类继承

+ 知识点：
  
  + 在 JavaScript 中，类是一种函数。
  
  + 类总是使用 `use strict`。 在类构造中的所有代码都将自动进入严格模式。
  
  + 类方法不可枚举。 类定义将 `"prototype"` 中的所有方法的 `enumerable` 标志设置为 `false`。
  
  + 类可以包括 getters/setters，计算属性（computed properties）等。
  
  + “类字段”是一种允许添加任何属性的语法。之前，我们的类仅具有方法。类字段重要的不同之处在于，它们会在每个独立对象中被设好，而不是设在 `User.prototype`。
  
  + - 执行 `super.method(...)` 来调用一个父类方法。
    - 执行 `super(...)` 来调用一个父类 constructor（只能在我们的 constructor 中）。
  
  + **继承类的 constructor 必须调用 `super(...)`，并且 (!) 一定要在使用 `this` 之前调用。**
  
  + 父类构造器总是使用父类的字段。当父类构造器在派生的类中被调用时，它会使用被重写的方法。（字段和方法的结果不同）
    
    - 对于派生类，类字段在 `super()` 后立刻初始化。
    - 对于基类（还未继承任何东西的那种），类字段在构造函数调用前初始化。
  
  + 想要扩展一个类：`class Child extends Parent`：
    
    - 这意味着 `Child.prototype.__proto__` 将是 `Parent.prototype`，所以方法会被继承。
  
  + 重写一个方法：
    
    - 我们可以在一个 `Child` 方法中使用 `super.method()` 来调用 `Parent` 方法。

---

### 9.3 静态属性和静态方法

+ 知识点：
  
  + 我们可以把一个方法赋值给类的函数本身，而不是赋给它的 `"prototype"`。这样的方法被称为 **静态的（static）**。以`static`关键字开头。与直接将其作为属性赋值的作用相同。在静态方法调用中的`this`值是类构造器本身。
  
  + 静态方法用于实现属于该类但不属于该类任何特定对象的函数。
  
  + 静态的属性也是可能的，它们看起来就像常规的类属性，但前面加有 `static`
  
  + 静态属性和方法是可被继承的。`extends` 让派生类构造函数的 `[[Prototype]]` 指向了基类构造函数。

---

### 9.4 私有的和受保护的属性和方法

+ 知识点：
  
  + **受保护的属性通常以下划线 `_` 作为前缀。**
  
  + 实现只读属性：只设置`getter`而不设置`setter`
  
  + 私有字段以 `#` 开头。JavaScript 确保我们只能从类的内部访问它们。

---

### 9.5 扩展内建类

+ 内建类指的是Array, Map等

+ 内建类没有静态方法继承

+ *// 内建方法将使用这个作为 `constructor`
  
  `static get [Symbol.species]() { return Array; }* }`

---

### 9.6 类检查 instanceof

+ `instanceof` 操作符用于检查一个对象是否属于某个特定的 class。同时，它还考虑了继承。

+ 内部算法
  
  + 如果有静态方法 `Symbol.hasInstance`，那就直接调用这个方法
  
  + 使用 `obj instanceOf Class` 检查 `Class.prototype` 是否等于 `obj` 的原型链中的原型之一。

+  `objA.isPrototypeOf(objB)`，如果 `objA` 处在 `objB` 的原型链中，则返回 `true`。

---

### 9.7 Mixin模式 (MIX IN)

+ 在 JavaScript 中，我们只能继承单个对象。每个对象只能有一个 `[[Prototype]]`。并且每个类只可以扩展另外一个类。

+ 通过将方法拷贝`Object.assign`到原型中来实现 mixin

---

### 10.1 错误处理 try catch

+ `throw` 操作符会生成一个 error 对象。

+ 技术上讲，我们可以将任何东西用作 error 对象。甚至可以是一个原始类型数据，例如数字或字符串，但最好使用对象，最好使用具有 `name` 和 `message` 属性的对象（某种程度上保持与内建 error 的兼容性）。

+ JavaScript 中有很多内建的标准 error 的构造器：`Error`，`SyntaxError`，`ReferenceError`，`TypeError` 等。

+ 对于内建的 error（不是对于其他任何对象，仅仅是对于 error），`name` 属性刚好就是构造器的名字。`message` 则来自于参数（argument）。

+ 如果我们不需要 error 对象，我们可以通过使用 `catch {` 而不是 `catch (err) {` 来省略它。

+ “再次抛出（rethrowing）”技术：
  
  1. `Catch` 捕获所有 error。
  2. 在 `catch (err) {...}` 块中，我们对 error 对象 `err` 进行分析。
  3. 如果我们不知道如何处理它，那我们就 `throw err`。

+ `try...catch` 结构可能还有一个代码子句（clause）：`finally`。
  
  如果它存在，它在所有情况下都会被执行：
  
  - `try` 之后，如果没有 error，
  - `catch` 之后，如果没有 error。

+ `finally` 子句适用于 `try...catch` 的 **任何** 出口。这包括显式的 `return`。`finally` 会在控制转向外部代码前被执行。

+ 全局catch：Node.JS 有 [`process.on("uncaughtException")`](https://nodejs.org/api/process.html#process_event_uncaughtexception)。在浏览器中，我们可以将一个函数赋值给特殊的 [window.onerror](https://developer.mozilla.org/zh/docs/Web/API/GlobalEventHandlers/onerror) 属性，该函数将在发生未捕获的 error 时执行。

---

### 10.2 自定义Error，扩展Error

- 我们可以正常地从 `Error` 和其他内建的 error 类中进行继承，。我们只需要注意 `name` 属性以及不要忘了调用 `super`。
- 我们可以使用 `instanceof` 来检查特定的 error。但有时我们有来自第三方库的 error 对象，并且在这儿没有简单的方法来获取它的类。那么可以将 `name` 属性用于这一类的检查。
- 包装异常是一项广泛应用的技术：用于处理低级别异常并创建高级别 error 而不是各种低级别 error 的函数。在上面的示例中，低级别异常有时会成为该对象的属性，例如 `err.cause`，但这不是严格要求的。
+ 习题

```javascript
class FormatError extands class SyntaxError {
    constuctor(message) {
        super(message);
        this.name = this.constructor.name;
    }
}
```

<mark>为什么可以用`this.constructor.name`?</mark>
