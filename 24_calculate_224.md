# 题目

**224. 基本计算器**

> 困难

给你一个字符串表达式 `s` ，请你实现一个基本计算器来计算并返回它的值。



注意:不允许使用任何将字符串作为数学表达式计算的内置函数，比如 `eval()` 。



**示例 1：**

```
输入：s = "1 + 1"
输出：2
```

**示例 2：**

```
输入：s = " 2-1 + 2 "
输出：3
```

**示例 3：**

```
输入：s = "(1+(4+5+2)-3)+(6+8)"
输出：23
```



**提示：**

- `1 <= s.length <= 3 * 105`
- `s` 由数字、`'+'`、`'-'`、`'('`、`')'`、和 `' '` 组成
- `s` 表示一个有效的表达式
- '+' 不能用作一元运算(例如， "+1" 和 `"+(2 + 3)"` 无效)
- '-' 可以用作一元运算(即 "-1" 和 `"-(2 + 3)"` 是有效的)
- 输入中不存在两个连续的操作符
- 每个数字和运行的计算将适合于一个有符号的 32位 整数

> leetcode地址：https://leetcode.cn/problems/basic-calculator/description/



# 思路：栈

可以用"栈"来实现，因为表达式中包含括号（且只有加减法，没有乘除法），所以可以使用栈来存储每个子表达式的值。本质上，需要延迟处理主表达式，直到完成对括号种的中间子表达式的求值



**算法如下：**

1、按逆序遍历字符串

2、如果字符是空格，则跳过

3、如果字符是数字，则计算出整数操作数，因为操作数可以由多个数字字符组成，字符串 "123" 表示数字 123，它可以被构造为：`123 => 120 + 3 => 100 + 20 + 3`，因为是逆序遍历字符串，所以要将读取的数字字符乘以 10 的幂并与当前数字相加，形成操作数

4、如果当前字符不是数字，则将整数操作数入栈

5、如果当前字符不是左括号`（`，则直接入栈

6、如果当前字符是左括号 `(`，这意味是一个子表达式的结束（由于是逆序，所以左括号成了表达式的结尾）。则需要从栈中弹出操作数和运算符来计算子表达式的值，直到弹出的字符是右括号为止。计算子表达式的结果后将结果入栈

7、遍历结束后，可能最左边是一个整数操作数，且没有用括号括起来，此时需要将整数操作数入栈

8、最后再计算一遍栈中的所有值即可



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

    // 整数操作数
    let operate = 0;
    // 整数操作数的进位
    let n = 0;

    // 逆序遍历
    for (let i = s.length - 1; i >= 0; i--) {
        let cur_char = s[i];

        // 空格跳过
        if (cur_char == ' ') {
            continue;
        }

        // 计算多个字符组成的整数操作数
        if (isDigit(cur_char)) {
            operate = (Math.pow(10, n) * Number(cur_char)) + operate;
            n += 1;
        } else {
            // 遇到非数字字符，则把操作数入栈
            if (n != 0) {
                stack.push(operate);
                operate = 0;
                n = 0;
            }

            // 遇到左括号，则计算括号内的子表达式的结果值，后重新入栈
            if (cur_char == '(') {
                // 计算括号内子表达式的值
                let res = evaluateExpr(stack);
                stack.push(res);
            } else {
                // 右括号 或 运算符，直接入栈
                stack.push(cur_char);
            }
        }
    }

    // 字符串左边是一个整数操作数，不是括号
    if (n != 0) {
        stack.push(operate);
    }

    return evaluateExpr(stack);

};

function evaluateExpr(stack) {
   let res = 0;
  
   if (stack.length) {
        // 如果是整数，当成左操作数（因为也可能是运算符）
        let topValue = stack[stack.length - 1];
        if (isDigit(topValue)) {
            stack.pop();
            res = topValue;
        }
   }

   // 栈为空 或 遇到右括号则停止循环
   while (stack.length && stack[stack.length - 1] !== ')') {
       // 运算符号
       let sign = stack.pop();
       // 第2个整数操作数
       const num = +stack.pop();

       // 因为栈存储的是从右到左的整数，所以取出来的值是从左到右的，可以直接相加减
       if (sign === '+') {
           res += num;
       } else {
           res -= num;
       }
   }
  
   // 去掉右括号
   if (stack.length) {
     stack.pop();  
   }
   
   return res;
}

// 判断字符是否是数字
function isDigit(c) {
    return !isNaN(Number(c));
}
```



**复杂度**

 * 时间复杂度：`O(N)`，其中 `N` 指的是字符串的长度
 * 空间复杂度：`O(N)`，其中 `N` 指的是字符串的长度

