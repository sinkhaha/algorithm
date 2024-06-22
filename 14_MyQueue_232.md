# 题目

**232. 用栈实现队列**

>简单

请你仅使用两个栈实现先入先出队列。队列应当支持一般队列的支持的所有操作（push、pop、peek、empty）：



实现 MyQueue 类：

* void push(int x) 将元素 x 推到队列的末尾
* int pop() 从队列的开头移除并返回元素
* int peek() 返回队列开头的元素
* boolean empty() 如果队列为空，返回 true ；否则，返回 false

**说明：**

* 你只能使用标准的栈操作 —— 也就是只有 push to top, peek/pop from top, size, 和 is empty 操作是合法的。
* 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。



**示例：**

```
输入：
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]

输出：
[null, null, null, 1, 1, false]

解释：
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false
```



**提示：**

* `1 <= x <= 9`
* 最多调用 100 次 push、pop、peek 和 empty
* 假设所有操作都是有效的 （例如，一个空的队列不会调用 pop 或者 peek 操作）



**进阶：**

- 你能否实现每个操作均摊时间复杂度为 `O(1)` 的队列？换句话说，执行 `n` 个操作的总时间复杂度为 `O(n)` ，即使其中一个操作可能花费较长时间。

>leetcode地址：https://leetcode-cn.com/problems/implement-queue-using-stacks



# 思路：用两个栈实现

用`两个数组(栈)`实现队列：

* 将一个栈 `inStack` 当作输入栈（数组头部为队列头，数据尾为队列尾），主要用于存放 `push` 进去的数据
* 另一个栈  outStack 当作输出栈，主要用于 `pop`、`peek` 操作时取队头的元素



**算法如下：**

* `push` 入列时，直接插入到输入栈 `inStack`  中
* `pop` 是移除并返回队头的元素：因为先入列的元素在 `inStack` 前面，所以需要先把 `inStack` 的元素依次弹出压入输出栈 `outStack` 中，这样 `outStack` 最后一个元素就是队头的元素数组，即队列 `pop` 返回的元素
* `peek` 到操作类似 `pop`，只不过 `peek` 不用移除元素

> 注意：把数据从 `inStack` 输入栈放入 `outStack` 输出栈后，不用重新放回 `inStack` 输出栈中，下次继续 `pop/peek` 时，直接从输出栈 `outStack` 取最后一个元素即可



## 代码

**rust 代码**

```rust
struct MyQueue {
    // 输入栈，数组头部为队列头，数据尾为队列尾
    in_stack: Vec<i32>,
    // 输出栈，用于peek和 pop，栈顶元素为队头元素（数组尾部为栈顶）
    out_stack: Vec<i32>,
}

/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl MyQueue {
    fn new() -> Self {
        Self {
            in_stack: Vec::new(),
            out_stack: Vec::new(),
        }
    }

     /**
     * 将元素 x 推到队列的末尾
     *
     * 时间O(1)
     */
    fn push(&mut self, x: i32) {
        self.in_stack.push(x);
    }

    /**
     * 从队列的开头移除并返回元素
     */
    fn pop(&mut self) -> i32 {
        // 输出栈为空，则需要把数据栈的数据搬到输出栈
        if self.out_stack.len() == 0 {
            while self.in_stack.len() > 0 {
                self.out_stack.push(self.in_stack.pop().unwrap());
            }
        }

        // pop 出输出栈的元素
        self.out_stack.pop().unwrap()
    }

    /**
     * 返回队列开头的元素
     */
    fn peek(&mut self) -> i32 {
         // 输出栈为空，则需要把数据栈的数据搬到输出栈
        if self.out_stack.len() == 0 {
            while self.in_stack.len() > 0 {
                self.out_stack.push(self.in_stack.pop().unwrap());
            }
        }

        // 返回输出栈的元素
        self.out_stack[self.out_stack.len() - 1]
    }
 
     /**
     * O(1)
     * 返回队列是否为空
     */
    fn empty(&self) -> bool {
        // 需要两个栈同时为空
        self.in_stack.len() == 0 && self.out_stack.len() == 0
    }
}
/**
 * Your MyQueue object will be instantiated and called as such:
 * let obj = MyQueue::new();
 * obj.push(x);
 * let ret_2: i32 = obj.pop();
 * let ret_3: i32 = obj.peek();
 * let ret_4: bool = obj.empty();
 */
```



**js 代码**

```javascript
class MyQueue {
    constructor() {
        this.inStack = []; // 输入栈，数组头部为队列头，数据尾为队列尾
        this.outStack = []; // 输出栈，用于peek和 pop，栈顶元素为队头元素（数组尾部为栈顶）
    }

    /**
     * 将元素 x 推到队列的末尾
     *
     * 时间O(1)
     */
    push(x) {
        this.inStack.push(x);
    }

    /**
     * 从队列的开头移除并返回元素
     */
    pop() {
         // 输出栈为空，则需要把数据栈的数据搬到输出栈
        if (!this.outStack.length) {
            while(this.inStack.length) {
                this.outStack.push(this.inStack.pop());
            }
        }

        // pop 出输出栈的元素
        return this.outStack.pop();
    }

    /**
     * 返回队列开头的元素
     */
    peek() {
       // 输出栈为空，则需要把数据栈的数据搬到输出栈
       if (!this.outStack.length) {
            while(this.inStack.length) {
                this.outStack.push(this.inStack.pop());
            }
        }

        // 返回输出栈的元素
        return this.outStack[this.outStack.length - 1];
    }

    /**
     * O(1)
     * 返回队列是否为空
     */
    empty() {
        // 需要两个栈同时为空
        return this.inStack.length === 0 && this.outStack.length == 0;
    }
}
```



## 复杂度

* 时间复杂度 `push`  和 `empty` 为 `O(1)`， `pop` 和 `peek` 均摊为 `O(1)`，对于每个元素，至多入栈和出栈各两次，故均摊复杂度为 `O(1)`
* 空间复杂度： `O(n)`。`n` 是操作总数。对于有 `n` 次 `push` 操作的情况，队列中会有 `n` 个元素，故空间复杂度为 `O(n)`

