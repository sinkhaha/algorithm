# 题目

**66. 加一**

> 简单

给定一个由整数组成的非空数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

示例 1:

```
输入: [1,2,3]
输出: [1,2,4]
解释: 输入数组表示数字 123。
```

示例 2:

```
输入: [4,3,2,1]
输出: [4,3,2,2]
解释: 输入数组表示数字 4321。
```

>来源：力扣（LeetCode）
>链接：https://leetcode-cn.com/problems/plus-one
>著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。



# 通用解法 ：数组的遍历

## 思路1

1、从后往前遍历

* 如果“当前元素 + 1”后等于10，则当前位为0，然后需要进1，下一位需要加上这个1
* 如果“当前元素 + 1”后小于10，则当前位为 “当前元素 + 1”

2、最后遍历结束，如果进位为1，需要把1添加到数组开头



**rust 代码如下**

```rust
impl Solution {
    pub fn plus_one(digits: Vec<i32>) -> Vec<i32> {
       let mut result: Vec<i32> = Vec::new();

        // 进位，默认1
        let mut carry = 1;

        for item in digits.iter().rev() {
            let sum = item + carry;
            let rst = sum % 10;

            // sum为10时，进位为1
            carry = sum / 10;

            result.push(rst);
        }

        // 最后一位可能为9，加1后为10，此时有进位，需要添加 1
        if carry == 1 {
            result.push(carry);
        }

        result.into_iter().rev().collect()
    }
}
```



## 思路2

因为题目规定了数组每个元素只能存1位数字，数字最大为9，所以当数组元素有9时，加1后需要进位，所以可以分为两种情况：一种是最后一位是9的情况，另一种是最后一位不是9的情况，此时实现方式如下



从后往前遍历，如果当前元素

1. `不是 9`:  当前位直接加1后返回
2. `是9`:  则当前位是0，继续向前遍历，依次循环步骤1和步骤2

3. 如果是类似 `99、999...` 之类数组，循环结束后还需要在首位加入一个进位的1



**rust 代码如下**

```rust
impl Solution {
    pub fn plus_one(digits: Vec<i32>) -> Vec<i32> {
        // digits不可变，创建一个新的vec数组
        let mut result: Vec<i32> = Vec::from(digits);
  
        // 从后往前遍历
        for i in (0..result.len()).rev() {
            if result[i] == 9 {
                // 当前位为9时，把当前位置为0，继续循环会进行进位操作
                result[i] = 0;
            } else {
                // 不为9，加1后直接返回
                result[i] += 1;
                return result;
            }
        }
        
        // 当遍历结束了，说明此时原数组的第一位肯定是9，例如99、999... 等特殊情况，在开头添加1
        result.insert(0, 1);

        result
    }
}
```

**js 代码如下**

```javascript
/**
 *
 * @param {number[]} digits
 * @return {number[]}
 */
var plusOne = function(digits) {
    for (let i = digits.length - 1; i >= 0; i--) {
        if (digits[i] !== 9) {
            digits[i]++;
            // 不为9，加1后直接返回
            return digits;
        } else {
            // 当前位为9时，把当前位置为0，继续循环会进行进位操作
            digits[i] = 0;
        }
    }
  
    // 在开头添加1
    digits.unshift(1);
  
    return digits;
};
```

## 复杂度

思路1 和 思路2 的复杂度如下

* 时间复杂度 `O(n)`， `n` 为数组长度

  > 最好时数组最后一位不是 9，只需要执行一次，即最好时间复杂度 `O(1)`
  >
  > 最坏时数组都是 9，需要循环 `n` 次，即最坏时间复杂度`O(n)`
  >
  > 平均时间复杂度 `O(n)`

* 空间复杂度 `O(1)`



# js专属解法: 大数相加

## 思路

利用 `ES10 `的 `bigint` 大数相加，直接转成数字后加1，再转成数组

## 代码

```javascript
/**
 *
 * @param {number[]} digits
 * @return {number[]}
 */
var plusOne = function(digits) {
    const num = BigInt(digits.join(''));    
    return (num + BigInt(1)).toString().split('');
};
```

## 复杂度

* 时间复杂度O(1)

 * 空间复杂度O(1)

