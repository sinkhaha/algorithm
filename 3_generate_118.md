# 题目

**118. 杨辉三角**
给定一个非负整数 numRows，生成「杨辉三角」的前 numRows 行。

在「杨辉三角」中，每个数是它左上方和右上方的数的和。

![](https://sink-blog-pic.oss-cn-shenzhen.aliyuncs.com/img/leetcode/20240613232112.png)


示例 1:
```
输入: numRows = 5
输出: [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]
```

示例 2:
```
输入: numRows = 1
输出: [[1]]
```

提示:
`1 <= numRows <= 30`



> leetcode地址：https://leetcode.cn/problems/pascals-triangle/description/



# 思路

先掌握杨辉三角的两个特点

1、每一行的开头都是 1，因为有对称性，所以每一行的结束也是 1

2、第 `i` 行的第 `j` 个数 = 第 `i - 1` 行的第 `j - 1`个数 + 第 `i - 1` 行的第 `j` 个数之和，即 `a[i][j] = a[i-1][j-1] + a[i-1][j]`，从下面的杨辉三角就可以直观的看出来

```js
1
1 1
1 2 1
1 3 3 1
1 4 6 4 1
```



**rust 代码如下**

```rust
impl Solution {
    pub fn generate(num_rows: i32) -> Vec<Vec<i32>> {
        let mut result: Vec<Vec<i32>> = Vec::with_capacity(num_rows as usize);

        for i in 0..num_rows {
            // 初始化当前行，i + 1个元素，每一个都初始化为1
            let mut row = vec![1; i as usize + 1];

            // 计算当前行每个元素的值
            // 每一行前面和最后面的1都不用处理，所以从1开始，到row.len() - 1结束
            for j in 1..(row.len() - 1) {
                // a[i][j] = a[i-1][j-1] + a[i-1][j]
                row[j] = result[i as usize - 1][j - 1] + result[i as usize - 1][j];
            }

            result.push(row);
        }

        result
    }
}

```



**js 代码如下**

```js
/**
 * @param {number} numRows
 * @return {number[][]}
 */
var generate = function(numRows) {
    let result = [];

    for (let i = 0; i < numRows; i++) {
        // 初始化当前行，i + 1个元素，每一个都初始化为1
        let row = new Array(i + 1).fill(1);
        
        //  计算当前行每个元素的值
        // 每一行前面和最后面的1都不用处理，所以从 1 开始，到 row.length - 1 结束
        for (let j = 1; j < row.length - 1; j++) {
            // a[i][j] = a[i-1][j-1] + a[i-1][j]
            row[j] = result[i - 1][j - 1] + result[i - 1][j];
        }

        result.push(row);
    }

    return result;

};
```



**复杂度**

* 时间复杂度 `O(n^2)`，`n` 为 `numRows`

* 空间复杂度 `O(1)`，不考虑返回值的占用空间

