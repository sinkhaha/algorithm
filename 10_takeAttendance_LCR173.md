# 题目
**LCR 173.点名**
> 简单

某班级 n 位同学的学号为 0 ~ n-1。点名结果记录于升序数组 records。假定仅有一位同学缺席，请返回他的学号。


**示例 1:**
>输入: records = [0,1,2,3,5]
输出: 4

**示例 2:**
>输入: records = [0, 1, 2, 3, 4, 5, 6, 8]
输出: 7


**提示：**
* `1 <= records.length <= 10000`

> leetcode地址 https://leetcode.cn/problems/que-shi-de-shu-zi-lcof/description/



# 思路

## 解法：数组

1、初始化一个 `n` 个元素的数组 `arr`，初始值都为 0 

2、遍历 `records` 数组，把数组值 `records[i]` 当成 `arr` 的下标，标记 `arr` 元素的值为 `1`， 即 `arr[records[i]] = 1`

3、遍历 `arr` 数组，元素值不为 `1` 的下标即为所求结果



**js 代码**

```js
/**
 * @param {number[]} records
 * @return {number}
 */
var takeAttendance = function(records) {
    let n = records.length;

    let arr = Array(n + 1).fill(0);

    for (let num of records) {
        if (arr[num] == 0) {
            arr[num] = 1;
        }
    }

    for (let i = 0; i <= n; i++) {
        if (arr[i] !== 1) {
            return i;
        }
    }
};
```

**复杂度**

* 时间复杂度 `O(n)`
* 空间复杂度 `O(n)`



## 解法：二分查找

因为题目规定了 `records` 是有序的，可以考虑用二分查找，时间复杂度优化为  `O(logN)`。



假设缺失的元素值为 `x`，如果 `x` 左边存在元素，则 `x` 左边的元素一定是连续的，也就是元素数值和下标是相等的，即 `nums[i] == i`；如果 `x` 右边存在元素，而其右边的元素因为 `x` 的缺失，一定是元素数值和下标不相等的， 即 `nums[i] != i`，我们把整个数组看成两部分

1. 左子数组 `nums[i] == i`
2. 右子数组 `nums[i] != i`

本质上就是要求 `nums[i] != i` 的第 1 个，也就是左边界



**二分算法**

1、初始化左边界 `left = 0` ，右边界 `right = len(nums) − 1`，表示 `[left, right]` 闭区间

2、二分：

* 当 `left <= right` 时循环二分，计算 left 和 right 的中点 mid，
* 若 `nums[mid] = mid` ，当前元素跟索引相等，说明 `mid` 前面的元素肯定都是完整的连续的元素，那应该去右子数组中查找，即缺少的数字肯定在 `[mid + 1, right]` 闭区间中，所以 `left = mid + 1`
* 若 `nums[mid] != mid` ，当前元素跟索引不相等，那应该去找左子数组中查找，即缺少的数字一定在 `[left, mid - 1]` 闭区间中，所以 `right = mid - 1`

3、 当 `left > right` 时就跳出循环，此时变量 `left` 和 `right` 分别指向 “右子数组的首元素” 和 “左子数组的末元素” ，因此返回 `left`  即可



**js 代码**

```js
var takeAttendance = function(records) {
    let left = 0;
    let right = records.length - 1;

    // 左闭右闭区间
    while (left <= right) {
        let mid = left + ((right - left) >> 1);
       
        if (records[mid] == mid) { 
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return left;
};
```

**rust 代码**

```rust
impl Solution {
    pub fn take_attendance(records: Vec<i32>) -> i32 {
        let mut left: i32 = 0;
        let mut right: i32 = (records.len() - 1) as i32;

        while left <= right {
            let mid = left + ((right - left) >> 1);

            if records[mid as usize] == mid {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }

        left
    }
}
```



**复杂度**

* 时间复杂度 `O(logN)`
* 空间复杂度 `O(1)`

