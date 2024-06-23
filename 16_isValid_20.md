# 题目
**20. 有效的括号**
> 简单

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。



有效字符串需满足：
1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。
3. 每个右括号都有一个对应的相同类型的左括号。



**示例 1：**
>输入：s = "()"
>
>输出：true

**示例 2：**
>输入：s = "()[]{}"
>
> 输出：true

**示例 3：**
>输入：s = "(]"
>
>输出：false

**提示：**
* `1 <= s.length <= 104`
* `s 仅由括号 '()[]{}' 组成`



# 思路

**算法如下**

1. 用栈来保存未匹配的左括号 `{、 [、 (` 

2. 从左到右依次遍历字符串
   * 当遇到左括号`{、 [、 (` 时，则将其压入栈中
   * 当遇到右括号`}、]、)`时，从栈顶取出一个左括号，看它们是否能够匹配；如果匹配就继续遍历剩下的字符串；如果遇到不能配对的右括号，或者栈中没有数据，则说明为非法格式
3. 当所有的括号都遍历完了，如果栈为空，则说明字符串为合法格式，否则，说明有未匹配的左括号，则为非法格式



# 代码

**rust 代码 **

```rust
use std::collections::HashMap;

impl Solution {
    pub fn is_valid(s: String) -> bool {
        let mut stack: Vec<char> = Vec::new();

        let mut left_right_map = HashMap::new();
        left_right_map.insert('(', ')');
        left_right_map.insert('[', ']');
        left_right_map.insert('{', '}');

        for c in s.chars() {
            // 左括号入栈
            if c == '(' || c == '[' || c == '{' {
                stack.push(c);
            } else {
                let left = stack.pop();
                if left == None {
                    return false;
                }
                
                // 检查右括号是否与之匹配的左括号
                if c != *left_right_map.get(&left.unwrap()).unwrap() {
                    return false;
                }
            }
        }

        stack.len() == 0
    }
}
```

**js 代码**

```js
/**
 * @param {string} s
 * @return {boolean}
 */
var isValid = function(s) {
    let stack = [];

    const leftRightMap = {
        '(': ')',
        '[': ']',
        '{': '}'
    }

    for (let c of s) {
        // 左括号入栈
        if (['(', '{', '['].includes(c)) {
            stack.push(c);
        } else {
            let left = stack.pop();
            // 检查右括号是否与之匹配的左括号
            if (leftRightMap[left] != c) {
                return false;
            }
        }
    }

    return stack.length == 0;
};
```



# 复杂度

 * 时间复杂度 `O(n)`
 * 空间复杂度 `O(1)`