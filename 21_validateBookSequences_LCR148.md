# 题目
**LCR 148. 验证图书取出顺序**
> 中等

现在图书馆有一堆图书需要放入书架，并且图书馆的书架是一种特殊的数据结构，只能按照 一定 的顺序 放入 和 拿取 书籍。



给定一个表示图书放入顺序的整数序列 putIn，请判断序列 takeOut 是否为按照正确的顺序拿取书籍的操作序列。你可以假设放入书架的所有书籍编号都不相同。



**示例 1：**
```
输入：putIn = [6,7,8,9,10,11], takeOut = [9,11,10,8,7,6]
输出：true
解释：我们可以按以下操作放入并拿取书籍：
push(6), push(7), push(8), push(9), pop() -> 9,
push(10), push(11),pop() -> 11,pop() -> 10, pop() -> 8, pop() -> 7, pop() -> 6
```


**示例 2：**

```
输入：putIn = [6,7,8,9,10,11], takeOut = [11,9,8,10,6,7]
输出：false
解释：6 不能在 7 之前取出。
```



**提示：**

* 0 <= putIn.length == takeOut.length <= 1000
* 0 <= putIn[i], takeOut < 1000
* putIn 是 takeOut 的排列。

注意：本题与主站 946 题相同：https://leetcode-cn.com/problems/validate-stack-sequences/

> leetcode地址：https://leetcode.cn/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/description/



# 思路

题意是根据 `putIn` 入栈的顺序，每 `push` 一个进栈后，可以继续选择要不要执行 `push` ，或者执行 `pop` 操作，最终按 `pop` 操作所弹出的元素的先后顺序做组成的序列要和 `takeOut` 序列的顺序一样，所以

* 因为书的编号不同，所以 `putIn` 和 `takeOut` 不会出现重复的元素，每个元素出栈的顺序是唯一的
* `takeOut` 是 `putIn` 的一个序列，`putIn` 和 `takeOut` 长度相同



假设这个 `putIn` 放入序列和 `takeOut` 取出序列是能对应上的，则说明这两个序列所有元素分别经过入栈和出栈操作后，每个元素入栈和出栈一次，最终栈会为空。所以可以利用一个辅助栈来模拟入栈和出栈操作，如果模拟成功，说明是 `takeOut` 是正确的取出操作

>  因为元素不重复，所以每个元素的出栈顺序是唯一的，只要出栈了顺序就固定下来了



**算法如下**

1、初始化辅助栈 `helpStack` ，初始化指向取出序列的指针 ` i ` 

2、遍历 `putIn` 放入序列

* 模拟入栈：将每个元素 `num` 依次入栈
* 模拟出栈：每次 `num` 入栈后，循环出栈，判断栈顶元素和取出序列当前元素是否相同，如果相同，即  `helpStack` 的栈顶元素 = 取出序列的当前元素 `takeOut[i] `"，则出栈， 一直循环到栈为空或栈顶元素不等于"取出序列的当前元素""为止

3、最后判断 `helpStack` 是否为空，为空则说明模拟出栈入栈是成功的，即 `takeOut` 取出序列合法



## 代码

**rust 代码**

```rust
impl Solution {
    pub fn validate_book_sequences(put_in: Vec<i32>, take_out: Vec<i32>) -> bool {
        let mut helper_stack: Vec<i32> = Vec::new();
        let mut i = 0;

        for num in put_in.iter() {
            helper_stack.push(*num);

            while !helper_stack.is_empty() && helper_stack[helper_stack.len() - 1] == take_out[i] {
                helper_stack.pop();
                i += 1;
            }
        }

        helper_stack.is_empty()
    }
}
```



**js 代码**

```js
/**
 * @param {number[]} putIn
 * @param {number[]} takeOut
 * @return {boolean}
 */
var validateBookSequences = function(putIn, takeOut) {
    let helperStack = [];
    let i = 0;

    for (let num of putIn) {
        // 模拟入栈
        helperStack.push(num);

        // 模拟出栈
        while (helperStack.length && takeOut[i] == helperStack[helperStack.length - 1]) {
            helperStack.pop();
            i++;
        }
    }

    return helperStack.length == 0;
};
```



## 复杂度

* 时间复杂度 `O(n)`

* 空间复杂度 `O(n)`

