# 题目

**150. 逆波兰表达式求值**

> 中等

给你一个字符串数组 `tokens` ，表示一个根据 [逆波兰表示法](https://baike.baidu.com/item/逆波兰式/128437) 表示的算术表达式。

请你计算该表达式。返回一个表示表达式值的整数。



**注意：**

- 有效的算符为 `'+'`、`'-'`、`'*'` 和 `'/'` 。
- 每个操作数（运算对象）都可以是一个整数或者另一个表达式。
- 两个整数之间的除法总是 **向零截断** 。
- 表达式中不含除零运算。
- 输入是一个根据逆波兰表示法表示的算术表达式。
- 答案及所有中间计算结果可以用 **32 位** 整数表示。



**示例 1：**

```
输入：tokens = ["2","1","+","3","*"]
输出：9
解释：该算式转化为常见的中缀算术表达式为：((2 + 1) * 3) = 9
```

**示例 2：**

```
输入：tokens = ["4","13","5","/","+"]
输出：6
解释：该算式转化为常见的中缀算术表达式为：(4 + (13 / 5)) = 6
```

**示例 3：**

```
输入：tokens = ["10","6","9","3","+","-11","*","/","*","17","+","5","+"]
输出：22
解释：该算式转化为常见的中缀算术表达式为：
  ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
= ((10 * (6 / (12 * -11))) + 17) + 5
= ((10 * (6 / -132)) + 17) + 5
= ((10 * 0) + 17) + 5
= (0 + 17) + 5
= 17 + 5
= 22
```



**提示：**

- `1 <= tokens.length <= 104`
- `tokens[i]` 是一个算符（`"+"`、`"-"`、`"*"` 或 `"/"`），或是在范围 `[-200, 200]` 内的一个整数

 

**逆波兰表达式：**

逆波兰表达式是一种后缀表达式，所谓后缀就是指算符写在后面。

- 平常使用的算式则是一种中缀表达式，如 `( 1 + 2 ) * ( 3 + 4 )` 。
- 该算式的逆波兰表达式写法为 `( ( 1 2 + ) ( 3 4 + ) * )` 。

逆波兰表达式主要有以下两个优点：

- 去掉括号后表达式无歧义，上式即便写成 `1 2 + 3 4 + * `也可以依据次序计算出正确结果。
- 适合用栈操作运算：遇到数字则入栈；遇到算符则取出栈顶两个数字进行计算，并将结果压入栈中

> Leetcode地址 https://leetcode.cn/problems/evaluate-reverse-polish-notation/description/
>
> 类似题目还有”[LCR 036. 逆波兰表达式求值](https://leetcode.cn/problems/8Zf90G/)“ 



# 思路

逆波兰表达式也称后缀表达式，它严格遵循「从左到右」的运算



**算法如下**

1、利用一个栈来存储数字

2、遍历表达式

* 遇到数字，则入栈

* 遇到符号，则取栈顶的两个元素进行计算，先出栈的元素是右操作数，后出栈的元素是左操作数，然后把计算结果重新入栈

3、最后整个栈只有一个元素，即逆波兰表达式的值



# 代码

**rust 代码**

```rust
impl Solution {
    pub fn eval_rpn(tokens: Vec<String>) -> i32 {
        let mut stack: Vec<i32> = Vec::new();

        for token in tokens {
            // 能解析为数字表示为数字，否则为字符
            if let Ok(val) = token.parse::<i32>() {
                stack.push(val);
            } else {
                // 注意先栈顶元素为右操作数
                let right_num = stack.pop().unwrap();
                let left_num = stack.pop().unwrap();

                let cal_rst = match &token as &str {
                    "+" => left_num + right_num,
                    "-" => left_num - right_num,
                    "*" => left_num * right_num,
                    "/" => left_num / right_num,
                    _ => panic!("token err"),
                };

                stack.push(cal_rst);
            }
        }

        // 栈所剩的元素即结果值
        stack.pop().unwrap()
    }
}
```



**js 代码** 

```js
/**
 * @param {string[]} tokens
 * @return {number}
 */
var evalRPN = function(tokens) {
    let stack = [];

    for (let item of tokens) {
        if (isNumber(item)) {
            // 注意要转成整数
            stack.push(parseInt(item));
        } else {
            // 注意先栈顶元素为右操作数
            let rigthNum = stack.pop();
            let leftNum = stack.pop();

            if (item == '+') {
                stack.push(leftNum + rigthNum);
            } else if (item == '-') {
                stack.push(leftNum - rigthNum);
            } else if (item == '*') {
                stack.push(leftNum * rigthNum);
            } else if (item == '/') {
                // 注意js要根据结果的正负决定向上取整还是向下取整
                let value = leftNum / rigthNum > 0 ? Math.floor(leftNum / rigthNum) : Math.ceil(leftNum / rigthNum);
                stack.push(value);
            }
        }
    }

    // 栈所剩的元素即结果值
    return stack.pop();
};

function isNumber(item) {
    return item != '+' && item != '-' && item != '*' && item != '/';
}
```



# 复杂度

* 时间复杂度 `O(n)`
 * 空间复杂度 `O(n)`

