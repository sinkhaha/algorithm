# 题目

**821. 字符的最短距离**

>简单

给你一个字符串 `s` 和一个字符 `c` ，且 `c` 是 `s` 中出现过的字符。

返回一个整数数组 `answer` ，其中 `answer.length == s.length` 且 `answer[i]` 是 `s` 中从下标 `i` 到离它 **最近** 的字符 `c` 的 **距离** 。

两个下标 `i` 和 `j` 之间的 **距离** 为 `abs(i - j)` ，其中 `abs` 是绝对值函数。

 

**示例 1：**

```
输入：s = "loveleetcode", c = "e"
输出：[3,2,1,0,1,0,0,1,2,2,1,0]
解释：字符 'e' 出现在下标 3、5、6 和 11 处（下标从 0 开始计数）。
距下标 0 最近的 'e' 出现在下标 3 ，所以距离为 abs(0 - 3) = 3 。
距下标 1 最近的 'e' 出现在下标 3 ，所以距离为 abs(1 - 3) = 2 。
对于下标 4 ，出现在下标 3 和下标 5 处的 'e' 都离它最近，但距离是一样的 abs(4 - 3) == abs(4 - 5) = 1 。
距下标 8 最近的 'e' 出现在下标 6 ，所以距离为 abs(8 - 6) = 2 。
```

**示例 2：**

```
输入：s = "aaab", c = "b"
输出：[3,2,1,0]
```

 

**提示：**

- `1 <= s.length <= 104`
- `s[i]` 和 `c` 均为小写英文字母
- 题目数据保证 `c` 在 `s` 中至少出现一次

>leetcode链接：https://leetcode-cn.com/problems/shortest-distance-to-a-character



# 思路 1

最先想到的思路

1. 遍历每个元素
2. 如果当前元素等于字符 `c`，那说明最短距离是 0 
3. 如果当前元素不等于字符 `c` ，则从该元素的左边和右边分别寻找第一个等于字符 `c` 的元素，分别计算该元素跟当前元素索引的距离，取其中小的即可



**js代码如下**

```js
/**
 * @param {string} s
 * @param {character} c
 * @return {number[]}
 */
var shortestToChar = function(s, c) {

   let result = [];

   for (let i = 0; i < s.length; i++) {
        if (s.charAt(i) == c) {
            result[i] = 0;
            continue;
        }
     
        let len = Number.MAX_SAFE_INTEGER;
        let l = i - 1;
        let r = i + 1;

        // 向左遍历
        while (l >= 0) {
            if (s.charAt(l) != c) {
                l--;
            } else {
                len = Math.min(len, i - l);
                break;
            }
        }
        
        // 向右遍历
        while (r <= s.length - 1) {
            if (s.charAt(r) != c) {
                r++;
            } else {
                len = Math.min(len, r - i);
                break;
            }
        }
            
        result[i] = len;
    }

    return result;

};
```



**复杂度**

* 时间复杂度 `O(n^2)`
* 空间复杂度 `O(n)`



# 思路 2

题目其实是要计算字符串 `S` 中每个字符`向左或向右`距离目标字符的最近距离

1. 从前往后遍历，假设当前索引为 `i`，记录上一个字符 `C` 出现的位置 `prev`，得出从`0 到 i`时，即在 `i` 左边中当前字符 `s[i]` 距离目标字符最近的距离，答案为`i - prev`

2. 从后往前遍历，假设当前索引为 `i`，记录上一个字符 `C` 出现的位置 `prev`，得出从`i-1 到 i` 时，即在 `i` 右边中当前字符 `s[i]` 距离目标字符最近的距离，答案为 `prev - i`

3. 求步骤1和步骤2的最小值即可

   

**js代码如下**

```javascript
/**
 * @param {string} s
 * @param {character} c
 * @return {number[]}
 */
var shortestToChar = function(s, c) {

    let res = [];
    
    // prev记录上一个字符C出现的位置
    // 因为S是[0, 10000]，所以直接初始化为-10000表示相对小即可
    let prev = -10001;
    
    for (let i = 0; i < s.length; i++) {
        if (s.charAt(i) == c) {
            prev = i;
        }
        res[i] = i - prev;
    }

    // 初始化为相对大值即可
    prev = 10001;
    for (let i = s.length - 1; i >= 0; i--) {
        if (s.charAt(i) == c) {
            prev = i;
        }
        // 取向左和向右中的最小值
        res[i] = Math.min(res[i], prev - i);
    }

    return res;
};
```

**rust代码如下**

```rust
impl Solution {  
    pub fn shortest_to_char(s: String, c: char) -> Vec<i32> {
        let chars: Vec<char> = s.chars().collect();

        let mut result = vec![chars.len() as i32; chars.len()];
  
        let mut prev = -10001i32;

        // 找 i 左边
        for i in 0..chars.len() {
            if chars[i] == c {
                prev = i as i32;
            }
            result[i] = i as i32 - prev;
        }

        // 找 i 右边
        prev = 10001i32;
        for i in (0..chars.len()).rev() {
            if chars[i] == c {
                prev = i as i32;
            }

            let right_len = prev - i as i32;

            // 取距离最小值
            result[i] = std::cmp::min(right_len, result[i]);
        }

        result
    }
}
```



**复杂度**

* 时间复杂度 `O(n)`,  `n` 为 `S` 的长度
* 空间复杂度 `O(n)`

