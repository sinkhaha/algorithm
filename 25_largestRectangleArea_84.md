# 题目

**84.柱状图中最大的矩形**

> 困难

给定 *n* 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。



求在该柱状图中，能够勾勒出来的矩形的最大面积。



**示例 1:**

![](https://sink-blog-pic.oss-cn-shenzhen.aliyuncs.com/img/leetcode20240701222704.png)



```
输入：heights = [2,1,5,6,2,3]
输出：10
解释：最大的矩形为图中红色区域，面积为 10
```



**示例 2：**

![](https://sink-blog-pic.oss-cn-shenzhen.aliyuncs.com/img/leetcode20240701222732.png)

```
输入： heights = [2,4]
输出： 4
```



**提示：**

- `1 <= heights.length <=105`
- `0 <= heights[i] <= 104`



# 思路

## 方法一：暴力解法

**js 代码**

> 提交后会超出时间限制

```js
/**
 * @param {number[]} heights
 * @return {number}
 */
var largestRectangleArea = function(heights) {
    let area = 0;
    
    let n = heights.length;

    for (let i = 0; i < n; i++) {
        // 最小宽度就是i自己
        let w = 1;

        // 从当前柱子 i 向左遍历，直到小于i的高度停止
        let left = i - 1;
        while (left >=0 && heights[left] >= heights[i] ) {
            w++;
            left--; // 向左遍历
        }

        // 从当前柱子 i 向右遍历，直到小于i的高度停止
        let right = i + 1;
        while (right < n && heights[right] >= heights[i]) {
            w++;
            right++; // 向右遍历
        }

        area = Math.max(w * heights[i], area);
    }

    return area;
};
```



**复杂度**

- 时间复杂度：*O*(N^2)， *N* 是输入数组的长度
- 空间复杂度：*O*(1)



## 方法二：单调递增栈



