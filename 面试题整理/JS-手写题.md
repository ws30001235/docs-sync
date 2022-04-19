# JS 手写题

##### 1. 实现`curry(）`

💛

接受一个函数参数，返回的函数是经过柯里化的函数。

```javascript
function curry(fn) {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn.apply(this, args);
        } else {
            return function(...otherArgs) {
                return curried.apply(this, args.concat(args2));
            }
        }
    }
}
```

##### 2.实现支持占位符的`curry() `

💛

`let _ = curry.placeholder;`

```javascript
function curry(func) {
    return function curried(...args) {
        const complete = args.length >= func.length && !args.slice(0, func.length).includes(curry.placeholder);
        if (complete) return func.apply(this, args);
        return function(...newArgs) {
            const replaced = args.map(arg => arg === curr.placeholder && newArgs.length ? newArgs.shift() : arg);
            return curried(...replaced, newArgs);
        }
    }
}
curry.placeholder = Symbol();
```

##### 3. 实现`Array.prototype.flat()`

###### 递归法 💧

```javascript
function flat(arr, depth = 1) {
  // your imeplementation here
  if (depth <= 0) return arr;
  let res = [];
  for (let item of arr) {
    if (Array.isArray(item)) {
      res = res.concat(flat(item, depth - 1));
    } else {
      res.push(item);
    }
  }
  return res;
}

```

###### 遍历法 💛

```javascript
function flat(arr, depth = 1) {
    while (arr.some(Array.isArray) && depth-- > 0) {
        arr = [].concat(...arr);
    }
    return arr;
}
```

##### 4. 实现基础的节流`throttle()`

💛

```javascript
function throttle(func, wait) {
    let waiting = false;
    let lastArgs = null;
    return function(...args) {
        if (!waiting) {
            func.apply(this, args);
            waiting = true;
            let timeout = () => setTimeout(() => {
                waiting = false;
                if (lastArgs) {
                    func.apply(this, lastArgs);
                    waiting = true;
                    lastArgs = null;
                    timeout();
                }
            }, wait);
            timeout();
        } else {
            lastArgs = args;
        }
    }
}
```

