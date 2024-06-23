# 题目
**155. 最小栈**
> 中等

设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。



实现 MinStack 类:

* MinStack() 初始化堆栈对象。
* void push(int val) 将元素val推入堆栈。
* void pop() 删除堆栈顶部的元素。
* int top() 获取堆栈顶部的元素。
* int getMin() 获取堆栈中的最小元素。



**示例 1:**
>输入：
>["MinStack","push","push","push","getMin","pop","top","getMin"]
>[[],[-2],[0],[-3],[],[],[],[]]
>
>输出：
>[null,null,null,null,-3,null,0,-2]
>
>解释：
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.

**提示：**
* `-31 <= val <= 231 - 1`
* pop、top 和 getMin 操作总是在 非空栈 上调用
* push, pop, top, and getMin最多被调用 3 * 10^4 次
> leetcode地址：https://leetcode.cn/problems/min-stack/description/




# 思路：辅助栈(最小栈)

可以利用“辅助栈”，在每个元素 `a` 入栈时把当前栈的最小值 `min` 存储起来，这样辅助栈的每个元素 `a`，表示的是栈中对应位置的元素到栈底元素的最小值；在任意一个时刻，栈内元素的最小值就存储在辅助栈的栈顶元素中。



**算法如下**

* 初始化一个数据栈 `stack`，存储栈的数据，也初始化一个辅助栈 `minStack`，也叫最小栈
* 当一个元素 `push` 入栈时，往数据栈放入元素；取当前辅助栈的栈顶存储的最小值，与当前元素比较得出最小值，将这个最小值插入辅助栈中
* 当一个元素 `pop` 出栈时，需要把辅助栈的栈顶元素也一并弹出
* 要获取当前栈的最小值，只需要返回辅助栈 `minStack` 的栈顶元素即可



# 代码

**rust 代码**

```rust
struct MinStack {
    stack: Vec<i32>,
    // 存栈已有数值的最小值
    min_stack: Vec<i32>,
}

/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl MinStack {
    fn new() -> Self {
        Self {
            stack: vec![],
            min_stack: vec![],
        }
    }

    fn push(&mut self, val: i32) {
        self.stack.push(val);

        let mut cur_min = val;

        // 入栈时，计算当前栈中的最小值curMin
        if self.min_stack.len() > 0 {
            cur_min = val.min(self.min_stack[self.min_stack.len() - 1]);
        }

        self.min_stack.push(cur_min);
    }

    fn pop(&mut self) {
        self.stack.pop();
        // 注意 辅助栈 也要pop
        self.min_stack.pop();
    }

    fn top(&self) -> i32 {
        self.stack[self.stack.len() - 1]
    }

    fn get_min(&self) -> i32 {
        self.min_stack[self.min_stack.len() - 1]
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * let obj = MinStack::new();
 * obj.push(val);
 * obj.pop();
 * let ret_3: i32 = obj.top();
 * let ret_4: i32 = obj.get_min();
 */
```





**js 代码**

```js

var MinStack = function() {
    this.stack = [];
    // 存栈已有数值的最小值
    this.minStack = [];
};

/** 
 * @param {number} val
 * @return {void}
 */
MinStack.prototype.push = function(val) {
    this.stack.push(val);

    // 入栈时，计算当前栈中的最小值curMin
    let curMin = val;
    if (this.minStack.length) {
        curMin = Math.min(val, this.minStack[this.minStack.length - 1]);
    }

    this.minStack.push(curMin);
};

/**
 * @return {void}
 */
MinStack.prototype.pop = function() {
    this.stack.pop();
    // 注意 辅助栈 也要pop
    this.minStack.pop();
};

/**
 * @return {number}
 */
MinStack.prototype.top = function() {
    return this.stack[this.stack.length - 1];
};

/**
 * @return {number}
 */
MinStack.prototype.getMin = function() {
    return this.minStack[this.minStack.length - 1];
};

/**
 * Your MinStack object will be instantiated and called as such:
 * var obj = new MinStack()
 * obj.push(val)
 * obj.pop()
 * var param_3 = obj.top()
 * var param_4 = obj.getMin()
 */
```



**复杂度**

* 时间复杂度 `O(1)`，因为栈的插入、删除与读取操作都是 `O(1)`，我们定义的每个操作最多调用栈操作两次
* 空间复杂度 `O(n)`

