# 题目

**面试题 16.26. 计算器**

> 中等

给定一个包含正整数、加(+)、减(-)、乘(*)、除(/)的算数表达式(括号除外)，计算其结果。



表达式仅包含非负整数，`+`， `-` ，`*`，`/` 四种运算符和空格 ` `。 整数除法仅保留整数部分。



**示例 1:**

```
输入: "3+2*2"
输出: 7
```

**示例 2:**

```
输入: " 3/2 "
输出: 1
```

**示例 3:**

```
输入: " 3+5 / 2 "
输出: 5
```

**说明：**

- 你可以假设所给定的表达式都是有效的。
- 请**不要**使用内置的库函数 `eval`。

> leetcode地址：https://leetcode.cn/problems/calculator-lcci/description/



# 思路

用栈来实现，因为乘、除法优先级大于加减法，所以把所有乘除法都先做运算然后把结果值存到栈中，如

* 如果遇到加号 `+` ，直接把数字放入栈中
* 如果遇到减号 `-`，则把数字的负值放入栈中
* 如果遇到乘号 `*` 和除号 `/`，则先运算后再把运算的结果放入栈中

最后计算栈中各个数字的加法即可得到结果



**算法如下**

1. 用变量 `preSign` 记录每个整之前的运算符（对于第一个整数，可以将其前面的运算符视为加号）
2. 因为需要识别出 `s` 字符串中整数（由多位字符组成），所以用一个 `num` 来记录这个多位字符组成的整数
3. 遍历字符串 `s`， 记当前字符为 `curChar`
4. 如果 `curChar` 是空格，则跳过
5. 如果 `curChar` 是数字字符，计算出最新的 `num` 整数值
6. 如果 `curChar` 不是数字字符（说明是运算符），或者已经是遍历到字符串的末尾（即 `num` 是最有一个整数）时，此时说明已经可以知道两个数（一个是栈顶元素，一个是 `num`）并且知道两个数之间的运算符 `preSign` 了，需要根据 `preSign` 来决定计算方式
   * 如果 `preSign` 是加号，将数字压入栈（最后遍历栈再计算即可）
   * 如果是减号，将数字的相反数压入栈
   * 如果乘除号，则弹出栈顶元素，计算栈顶元素与数字的结果，并存入栈中
7. 处理完该整数字 `num` 后，把 `preSign` 更新为当前的运算符，`num` 重置为 `0`
8. 遍历完字符串 `s` 后，将栈中元素累加，即为该字符串表达式的值



# 代码

**js 代码**

```js
/**
 * @param {string} s
 * @return {number}
 */
var calculate = function(s) {
    s = s.trim();
    
    let stack = [];
  
    // 整数前的符号
    let preSign = '+';
    // 字符串中的每个整数
    let num = 0;

    for (let i = 0; i < s.length; i++) {
        let curChar = s[i];

        if (curChar == ' ') {
            continue;
        }

        // 计算多位数字字符组成的整数
        if (isDigit(curChar)) {
            num = num * 10 + curChar.charCodeAt() - '0'.charCodeAt();
        }

        // 如果遇到运算符，或者是 字符串末尾，则需要计算 num 跟栈顶元素的值
        if (!isDigit(curChar) || i == s.length - 1) {
            switch (preSign) {
                case '+':
                    stack.push(num)
                    break;
                case '-':
                    stack.push(-num);
                    break;
                case '*':
                    stack.push(stack.pop() * num);
                    break;
                case '/':
                    let rst = stack.pop() / num;
                    // 注意js，正负的取整方法
                    stack.push(rst > 0 ? Math.floor(rst) : Math.ceil(rst));
                    break;        
            } 
            // 设置为当前的运算符，等参与下一个整数的运算
            preSign = curChar;
            // 下一个整数重置为0，重新提取
            num = 0;
        }
    }

    let rst = 0;
    while (stack.length) {
        rst += stack.pop();
    }

    return rst;
};

function isDigit(c) {
    return !isNaN(Number(c));
}
```



**rust 代码**

```rust
impl Solution {
   pub fn calculate(mut s: String) -> i32 {
       s = s.trim().to_string();

       let mut stack: Vec<i32> = vec![];
       let mut num: i32 = 0;
       let mut pre_sign = '+';

       for (index, cur_char) in s.char_indices() {
           if cur_char == ' ' {
               continue;
           }
   
           if cur_char.is_digit(10) {
               num = num * 10 + cur_char.to_digit(10).unwrap() as i32;
           }

           if !cur_char.is_digit(10) || index == s.len() - 1 {
               match pre_sign {
                   '+' => stack.push(num),
                   '-' => stack.push(-num),
                   '*' => {
                       let val = stack.pop().unwrap();
                       stack.push(val * num)
                   }
                   '/' => {
                      let val = stack.pop().unwrap();
                       stack.push(val / num)
                  }
                   _ => panic!("cur_char error"),
              }
               pre_sign = cur_char;
              num = 0;
          }
      }

       let mut result = 0;
       for i in 0..stack.len() {
           result += stack[i];
       }

       result
    }
}
```



# 复杂度

* 时间复杂度：`O(N)`

* 空间复杂度：`O(N)`

