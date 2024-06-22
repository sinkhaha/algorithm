# 题目

**394. 字符串解码**

>中等

给定一个经过编码的字符串，返回它解码后的字符串。



编码规则为: k[encoded_string]，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。



你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。



此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2[4] 的输入。



**示例 1：**

```
输入：s = "3[a]2[bc]"
输出："aaabcbc"
```

**示例 2：**

```
输入：s = "3[a2[c]]"
输出："accaccacc"
```

**示例 3：**

```
输入：s = "2[abc]3[cd]ef"
输出："abcabccdcdcdef"
```

**示例 4：**

```
输入：s = "abc3[cd]xyz"
输出："abccdcdcdxyz"
```

>leetcode地址：https://leetcode-cn.com/problems/decode-string



# 思路：栈

利用栈解题

* 遍历字符串
* 遇到 `"数字"` 、`"["`、`"字母" `入栈（即不是右括号`"]"`的都入栈）
* 遇到右括号 `"]"` 则出栈，一直出栈直到不等于左括 `"["` 时停止出栈
* 接着取出当前 `"[ ]"` 括号之间要的字符串 `str`，再取得 `"["` 前面的数字，计算 `str` 重复次数后的字符串`pushStr`，然后 `pushStr` 再入栈（因为外层可能还有 `"[]"` 包裹，等到下一次循环继续处理）
* 最后把栈的元素拼接起来即可




注意

* 字母前面的数字可能有"多位"
* `"[ [] ]"` 存在嵌套



## 代码

**rust 代码**

```rust
impl Solution {
   
    pub fn decode_string(s: String) -> String {
        let mut stack = Vec::new();
        
        // 临时存储当前 [ 前连续的数字
        let mut pre_num = String::from("");

        for c in s.chars() {
            // 处理连续数字
            if c.is_digit(10) {
                pre_num.push(c);
                continue;
            }

            // 数字入栈
            if pre_num.len() > 0 {
                // 这里要clone，不然pre_num所有权会转移
                stack.push(pre_num.clone());
                // 注意入栈后清空 
                pre_num.clear();
            }

            // 不等于 ] 入栈
            // 注意字符char是单引号
            if c != ']' {
                stack.push(c.to_string());
                continue;
            }

            // 获取中括号[]之间的字符串，即出栈，直到 [ 就停止
            let mut string = String::from("");
            while stack.len() > 0 && stack[stack.len() - 1] != "[".to_string() {
                string = format!("{}{}", stack.pop().unwrap(), string);
            }

            // pop 出 [
            stack.pop();

            // pop 出 数字
            let repeat_count_str = stack.pop().unwrap();
            let repeat_count = repeat_count_str.parse::<usize>().unwrap();
   
            // 输出重复次数的字符串
            let repeat_str = string.repeat(repeat_count);

            // 重新push进去，因为可能是内部的重复，还需要外部继续计算
            stack.push(repeat_str);
        }

        stack.join("")
    }
}
```



**js 代码**

```javascript
/**
 * @param {string} s
 * @return {string}
 */
var decodeString = function(s) {
    let stack = [];

    // 临时存储当前 [ 前连续的数字，注意可能有多次
    let preNum = ''; 

    for (let c of s) {
        // 处理连续数字
        if (Number.isInteger(+c)) {
            preNum += c;
            continue;
        }

        // 数字入栈
        if (preNum) {
            stack.push(+preNum);
            // 注意入栈后清空 
            preNum = '';
        }

        // 不等于 ] 直接入栈
        if (c != ']') {
            stack.push(c);
            continue;
        }
 
        // 获取中括号[]之间的字符串，即出栈，直到 [ 就停止
        let str = '';
        while (stack.length && stack[stack.length - 1] != '[') {
            // 注意pop出来的数字在前面 
            str = stack.pop() + str;
        }
 
        // pop 出 "["
        stack.pop();

        // pop 出 数字
        let repeatCount = +stack.pop();

        let repeatStr = str.repeat(repeatCount);
        
        // 重新push进去，因为可能是内部的重复，还需要外部继续计算
        stack.push(repeatStr);
    }

    return stack.join('');
};
```

## 复杂度

* 时间复杂度 `O(N)`，`N` 为  `s` 的长度
* 空间复杂度 `O(N)`

