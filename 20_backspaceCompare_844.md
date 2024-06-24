# 题目
**844. 比较含退格的字符串**
> 简单

给定 s 和 t 两个字符串，当它们分别被输入到空白的文本编辑器后，如果两者相等，返回 true 。# 代表退格字符。

**注意：** 如果对空文本输入退格字符，文本继续为空。

**示例 1：**
>输入：s = "ab#c", t = "ad#c"
>输出：true
>解释：s 和 t 都会变成 "ac"。

**示例 2：**
>输入：s = "ab##", t = "c#d#"
>输出：true
>解释：s 和 t 都会变成 ""。

**示例 3：**
>输入：s = "a#c", t = "b"
>输出：false
>解释：s 会变成 "c"，但 t 仍然是 "b"。



**提示：**
* `1 <= s.length, t.length <= 200`
* s 和 t 只含有小写字母以及字符 '#'

**进阶：**
* 你可以用 O(n) 的时间复杂度和 O(1) 的空间复杂度解决该问题吗？

> leetcode地址：https://leetcode.cn/problems/backspace-string-compare/description/



# 思路

## 解法1: 栈

1. 分别使用栈存储 `s` 和 `t` 的每个字符

2. 遇到 `“#”` 则出栈
3. 最后比较两个栈中的字符是否一样即可



**rust 代码**

```rust
impl Solution {
    pub fn backspace_compare(s: String, t: String) -> bool {
        if s == t {
            return true;
        }

        deal(s) == deal(t)
    }
}

// 使用栈存储每次输入的字符，遇到#则出栈
pub fn deal(s: String) -> String {
    let mut stack = vec![];

    for c in s.chars() {
        if c == '#' {
            stack.pop();
        } else {
            stack.push(c);
        }
    }

    stack.into_iter().collect()
}
```



**js 代码**

```js
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var backspaceCompare = function(s, t) {
    if (s === t) {
        return true;
    }
    
    return deal(s) === deal(t);
};

/**
 * 使用栈存储每次输入的字符，遇到#则出栈
 * @param {*} 
 */
function deal(s) {
    const arr = s.split('');
    let result = [];

    arr.forEach(val => {
        if (val === '#') {
            result.pop(); 
        } else {
            result.push(val);
        }
    });
    return result.join('');
}

```



**复杂度**

* 时间复杂度 `O(n + m)`，`n` 为 `s` 的长度，`m` 为 `t` 的长度
 * 空间复杂度 `O(n + m)`



## 解法2: 双指针

可以用双指针解法把空间复杂度优化为 `O(1)`，因为 `#` 号会消除左边的字符，对右边的字符没影响，所以双指针可以从后往前遍历字符串，算法如下

1、用两个指针 `sPointer` 和 `tPointer` 分别指向 `s` 和 `t` 的末位字符

2、再用两个变量` skipS` 和 `skipT` 存储 `s` 和 `t` 中当前待起消除作用的 `#` 的数量，可以理解为当前待删除的字符数量（注意如 `ab##`，消除后结果是空字符串，两个 `#` 会消除两个字符）

3、分别从后往前遍历字符串 `s` 和 `t`，逐位比较每个字符是否相等，如果需要 `#`，需要先执行消除操作，例如遍历 s 时

* 当前字符如果是 `#`，说明需要删除掉一个字符，此时 `skipS` 需要加 `1`，表示要消除一个字符，`sPointer ` 向左移动一位
* 若当前字符不是 `#`，但是 `skipS` 不为 `0`，则需要消除字符， `sPointer` 向左移一位，`skip_s` 减 `1`
* 若当前字符不是 `#` ，且 `skipS` 为 `0` ，则表示当前字符不会被消除，此时可以用来跟 `t` 字符串的当前字符做比较，如果不相等，则返回 `false`，相等则继续遍历下一个字符进行比较

4、最后遍历结束了，如果没有不相等，说明 `s` 和 `t` 经过消除后，所剩下的字符都相等



**js 代码**

```js
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var backspaceCompare = function(s, t) {
    let sPointer = s.length - 1;
    let tPointer = t.length - 1;

    let skipS = 0;
    let skipT = 0;

    while (sPointer >= 0 || tPointer >= 0) {
        while (sPointer >= 0) {
            if (s[sPointer] == '#') {
                skipS++;
                sPointer--;
            // 一个一个消除  
            } else if (skipS > 0) {
                skipS--;
                sPointer--;
            } else {
                break;
            }
        }

        while (tPointer >= 0) {
            if (t[tPointer] == '#') {
                skipT++;
                tPointer--;
            } else if (skipT > 0) {
                skipT--;
                tPointer--;
            } else {
                break;
            }
        }

        if (s[sPointer] != t[tPointer]) {
            return false
        }

        sPointer--;
        tPointer--;
    }

    return true;
    
};

```



**复杂度**

* 时间复杂度 `O(n + m)`，`n` 为 `s` 的长度，`m` 为 `t` 的长度
* 空间复杂度 `O(1)`

