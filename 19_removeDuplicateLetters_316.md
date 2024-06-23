# 题目

**316. 去除重复字母**

> 中等

给你一个字符串 `s` ，请你去除字符串中重复的字母，使得每个字母只出现一次。需保证 **返回结果的字典序最小**（要求不能打乱其他字符的相对位置）。



**示例 1：**

```
输入：s = "bcabc"
输出："abc"
```

**示例 2：**

```
输入：s = "cbacdcbc"
输出："acdb"
```

 

**提示：**

- `1 <= s.length <= 104`
- `s` 由小写英文字母组成



**注意：**该题与 1081 https://leetcode-cn.com/problems/smallest-subsequence-of-distinct-characters 相同



# 思路：栈

1、维护一个结果栈

2、维护一个哈希表用于存储每个字符剩余出现的次数

3、遍历字符串

* 减少维护的计数，如果这个字符在字符串只出现过一次，则把这个字符放入栈中
* 如果当前字符比“栈顶字符”小且栈顶字符 在后续待遍历的字符串中还有出现，则一直循环，则栈顶元素需要出栈，等待该字符下一次入栈，最后把当前字符入栈（因为当 “当前字符” < “栈顶字符” 且“栈顶字符出现过多次” 时，说明按顺序“{当前字符}{栈顶字符}” 的字典序列一定是比 “{栈顶字符}{当前字符}” 大的，所以此时栈顶字符不应该出现在结果中）



# 代码

**rust 代码**

```rust
use std::collections::HashMap;

impl Solution {
    pub fn remove_duplicate_letters(s: String) -> String {
        // 存放字符剩余出现的次数，会随着s的遍历逐渐减少
        // key是字符，value是每个字符出现的次数
        let mut count_map: HashMap<char, i32> = HashMap::new();

        for c in s.chars() {
            if count_map.contains_key(&c) {
                count_map.insert(c, count_map.get(&c).unwrap() + 1);
            } else {
                count_map.insert(c, 1);
            }
        }

        // 存放最小的结果序列
        let mut result_stack = vec![];

        for c in s.chars() {
            count_map.insert(c, count_map.get(&c).unwrap() - 1);

             // 结果序列有了，直接跳过
            if result_stack.contains(&c) {
                continue;
            }

            // 如果"当前字符"<"结果栈顶字符"，且“结果栈顶字符”在后面还会出现，则 pop “结果栈顶字符”
            // 因为需要序列 “{当前字符}{结果栈顶字符}“ 会小于 “{结果栈顶字符}{当前字符}”
            while result_stack.len() > 0 && c < result_stack[result_stack.len() - 1] {
                let top = result_stack[result_stack.len() - 1];
              
                // 说明 top 这个字符在后面不会出现了，需要把它保留在结果中
                if *count_map.get(&top).unwrap() == 0 {
                    break;
                }
                
                 // 说明 top 字符后面还会出现，这里 pop 出来，先不把它算进结果中
                result_stack.pop().unwrap();
            }

            result_stack.push(c);
        }   

        result_stack.iter().collect::<String>()
    }

}
```



**js 代码** 

```js
/**
 * @param {string} s
 * @return {string}
 */
var removeDuplicateLetters = function(s) {
    // 存放字符剩余出现的次数，会随着s的遍历逐渐减少
    // key是字符，value是每个字符出现的次数
    let countMap = {};

    for (let c of s) {
        countMap[c] = countMap[c] ? countMap[c] + 1 : 1;
    }

    // 存放最小的结果序列
    let resultStack = [];

    for (let c of s) {
        countMap[c] = countMap[c] - 1;

        // 结果序列有了，直接跳过
        if (resultStack.includes(c)) {
            continue;
        }

        // 如果"当前字符"<"结果栈顶字符"，且“结果栈顶字符”在后面还会出现，则 pop “结果栈顶字符”
        // 因为需要序列 “{当前字符}{结果栈顶字符}“ 会小于 “{结果栈顶字符}{当前字符}”
        while (resultStack.length && c < resultStack[resultStack.length - 1]) {
            const top = resultStack[resultStack.length - 1];

            // 说明 top 这个字符在后面不会出现了，需要把它保留在结果中
            if (countMap[top] == 0) {
                break;
            }

            // 说明 top 字符后面还会出现，这里pop 出来，先不把它算进结果中
            resultStack.pop();
        }

        resultStack.push(c);
    }

    
    return resultStack.join('');
};
```



# 复杂度

 * 时间复杂度 `O(n)` ，`n` 为字符串度长度
 * 空间复杂度 `O(n)`

   