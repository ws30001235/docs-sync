## leetcode踩坑&API记录 etc..

考虑32位有符号位整数，数值范围是[−2^31, 2^31−1]。即最小值的绝对值是等于最大值+1的。`leetcode29`中需要考虑`min/(-1)`的边界情况。



## 运算符，API

#### 位运算

- `&` 两个位都为1时，结果才为1
- `|` 两个位都为0时，结果才为0
- `^` 两个位相同为0，相异为1
- `~` 0变1，1变0
- `<<` 按位左移
- `>>` 按位右移
- `>>>` 按位无符号右移



## 一些坑

- 滑动窗口可能不能解决有负数的案例，因为缩小窗口时，值可能是变大的
- `Math.floor`会将-0.2 取整为 -1. 当我们需要去除数字后面的小数点时，应该用`parseInt`



## 数据结构实现

实现一个优先队列（堆）

这里`comparator= (a, b) => a > b` 是大根堆

```javascript
const top = 0;
const parent = i => ((i + 1) >>> 1) - 1;
const left = i => (i << 1) + 1;
const right = i => (i + 1) << 1;

class PriorityQueue {
    constructor(comparator= (a, b) => a < b) {
        this._heap = [];
        this._comparator = comparator;
    }
    size() {
        return this._heap.length;
    }
    isEmpty() {
        return this.size() == 0;
    }
    peek() {
        return this._heap[top];
    }
    push(...values) {
        values.forEach(value => {
            this._heap.push(value);
            this._siftUp();
        });
        return this.size();
    }
    pop() {
        const poppedVal = this.peek();
        const bottom = this.size() - 1;
        if (bottom > top) {
            this._swap(top, bottom);
        }
        this._heap.pop();
        this._siftDown();
        return poppedVal;
    }
    replace(value) {
        const replacedValue = this.peek();
        this._heap[top] = value;
        this.siftDown();
        return replacedValue;
    }
    _greater(i, j) {
        return this._comparator(this._heap[i], this._heap[j]);
    }
    _swap(i, j) {
        [this._heap[i], this._heap[j]] = [this._heap[j], this._heap[i]];
    }
    _siftUp() {
        let node = this.size() - 1;
        while (node > top && this._greater(node, parent(node))) {
            this._swap(node, parent(node));
            node = parent(node);
        }
    }
    _siftDown() {
        let node = top;
        while (
            (left(node) < this.size() && this._greater(left(node), node)) || 
            (right(node) < this.size() && this._greater(right(node), node))
        ){
            let maxChild = (right(node) < this.size() && this._greater(right(node), left(node))) ? right(node) : left(node);
        this._swap(node, maxChild);
        node = maxChild;
        }
    }
}
```

