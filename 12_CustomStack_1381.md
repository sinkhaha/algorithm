# 题目

**1381. 设计一个支持增量操作的栈**

>中等

请你设计一个支持下述操作的栈。

实现自定义栈类 CustomStack ：

* CustomStack(int maxSize)：用 maxSize 初始化对象，maxSize 是栈中最多能容纳的元素数量，栈在增长到 maxSize 之后则不支持 push 操作。
* void push(int x)：如果栈还未增长到 maxSize ，就将 x 添加到栈顶。
* int pop()：弹出栈顶元素，并返回栈顶的值，或栈为空时返回 -1 。
* void inc(int k, int val)：栈底的 k 个元素的值都增加 val 。如果栈中元素总数小于 k ，则栈中的所有元素都增加 val 。


示例：

```
输入：
["CustomStack","push","push","pop","push","push","push","increment","increment","pop","pop","pop","pop"]
[[3],[1],[2],[],[2],[3],[4],[5,100],[2,100],[],[],[],[]]

输出：
[null,null,null,2,null,null,null,null,null,103,202,201,-1]

解释：
CustomStack customStack = new CustomStack(3); // 栈是空的 []
customStack.push(1);                          // 栈变为 [1]
customStack.push(2);                          // 栈变为 [1, 2]
customStack.pop();                            // 返回 2 --> 返回栈顶值 2，栈变为 [1]
customStack.push(2);                          // 栈变为 [1, 2]
customStack.push(3);                          // 栈变为 [1, 2, 3]
customStack.push(4);                          // 栈仍然是 [1, 2, 3]，不能添加其他元素使栈大小变为 4
customStack.increment(5, 100);                // 栈变为 [101, 102, 103]
customStack.increment(2, 100);                // 栈变为 [201, 202, 103]
customStack.pop();                            // 返回 103 --> 返回栈顶值 103，栈变为 [201, 202]
customStack.pop();                            // 返回 202 --> 返回栈顶值 202，栈变为 [201]
customStack.pop();                            // 返回 201 --> 返回栈顶值 201，栈变为 []
customStack.pop();                            // 返回 -1 --> 栈为空，返回 -1
```

提示：

* 1 <= maxSize <= 1000
* 1 <= x <= 1000
* 1 <= k <= 1000
* 0 <= val <= 100
* 每种方法 increment，push 以及 pop 分别最多调用 1000 次

>leetcode地址：https://leetcode-cn.com/problems/design-a-stack-with-increment-operation



# 解法1：数组实现栈

## 思路

最暴力的方法是直接用`数组`模拟栈，`push` 和 `pop` 时只需要操作数组原生的方法即可，`increment` 时为栈中的每个元素都加上增量的值



## 代码

**js 代码**

```javascript
/**
 * @param {number} maxSize
 */
var CustomStack = function(maxSize) {
    this.maxSize = maxSize;
    this.stack = []; // 数组前面的元素是栈底
};

/** 
 * @param {number} x
 * @return {void}
 */
CustomStack.prototype.push = function(x) {
    if (this.stack.length >= this.maxSize) {
        return;
    }
    this.stack.push(x);
};

/**
 * @return {number}
 */
CustomStack.prototype.pop = function() {
    return this.stack.length ? this.stack.pop() : -1;
};

/** 
 * @param {number} k 
 * @param {number} val
 * @return {void}
 */
CustomStack.prototype.increment = function(k, val) {
    // 注意：当有增量时，取栈中的每个元素加上增量的值再次存入栈中
    let length = Math.min(k, this.stack.length);
    for (let i = 0; i < length; i++) {
        this.stack[i] += val;
    }
};

/**
 * Your CustomStack object will be instantiated and called as such:
 * var obj = new CustomStack(maxSize)
 * obj.push(x)
 * var param_2 = obj.pop()
 * obj.increment(k,val)
 */
```

## 复杂度

* 时间复杂度 `push: O(1)`、`pop: O(1)`、`increment：O(N) `，`N` 为 `min(k， 栈中元素的个数)`
* 空间复杂度 `O(N)`，`N` 为 `maxSize`





# 解法2：空间换时间

## 思路

 解法 2 是 解法 1 的优化，在解法 1 的基础上把 `increment` 的时间复杂度从 `O(N)` 降为 `O(1)`。



**核心:** 是增量不实时计算，而是先保存增量，等到 `pop`  获取数据时，才实时把增量添加到 `pop` 出来的值

> 类似按需加载，而不是在 `increment` 时为栈的每个元素都添加增量



**空间换时间**

* 使用一个 `stack` 数组存储栈中的值
* 使用一个哈希表 `hashMap` 保存下标和增量的关系，`key` 是 `stack` 数组元素的下标，`value` 是"当前下标的值"要增加的增量值，表示 `stack[key]` 元素本身以及下标小于 `key` 的元素在 `pop` 时要增加的增量
* 在 `increment` 方法中只保存下标和增量关系到哈希表
* 在 `pop` 时才去为"弹出的元素"加上增量，`pop` 后的元素对应的下标 `top_index` 的增量要置为 `0`

注意：`pop` 栈顶元素后，如果 `pop` 出的元素有增量（假设栈顶元素的下标为 `top_index` ），则哈希表中要为 `top_index - 1` 这个下标增加一样的增量。因为哈希表保存的增量并不是键 `key` 这个下标对应元素自己的增量 ，而是小于等于 `key` 的所有下标对应元素的增量，现在栈顶元素出栈了，下一个充当栈顶的元素只有加上出栈的栈顶元素的增量，这样每次出栈，才能把小于 `key` 的所有元素都增加一样的增量）



## 代码

**rust 代码**

```rust

```



**js 代码**

```javascript
/**
 * @param {number} maxSize
 */
var CustomStack = function(maxSize) {
    this.maxSize = maxSize;
    // 用数组保存栈的数据
    this.stack = [];
    // key是数组下标，value是增量
    this.hashMap = new Map();
};

/** 
 * @param {number} x
 * @return {void}
 */
CustomStack.prototype.push = function(x) {
    if (this.stack.length < this.maxSize) {
        this.stack.push(x);
    }
};

/**
 * @return {number}
 */
CustomStack.prototype.pop = function() {
    let topIndex = this.stack.length - 1;
    if (topIndex < 0) {
        return -1;
    }

    // 栈顶元素的增量，取出来后要清空，然后 top - 1 的索引要保存增量
    let incValue = this.hashMap.get(topIndex) || 0;
    // 删掉增量
    this.hashMap.delete(topIndex);

    // 栈顶元素加上增量
    let result = this.stack.pop() + incValue;

    let newTopIndex = topIndex - 1;
    // 注意：为 topIndex - 1 的索引保存增量，表示 topIndex - 1 出栈时也需要加上增量
    this.hashMap.set(newTopIndex, (this.hashMap.get(newTopIndex) || 0) + incValue);

    return result;
};

/** 
 * @param {number} k 
 * @param {number} val
 * @return {void}
 */
CustomStack.prototype.increment = function(k, val) {
    let index = Math.min(this.stack.length, k) - 1;
    if (index < 0) {
        return;
    }

    this.hashMap.set(index, (this.hashMap.get(index) || 0 ) + val);
};

/**
 * Your CustomStack object will be instantiated and called as such:
 * var obj = new CustomStack(maxSize)
 * obj.push(x)
 * var param_2 = obj.pop()
 * obj.increment(k,val)
 */
```

## 复杂度

* 时间复杂度 `push: O(1)`、`pop: O(1)`、`increment: O(1) `，`N` 为 `min(K， 栈中元素的个数)`
* 空间复杂度 `O(N)`，`N` 为 `maxSize`

