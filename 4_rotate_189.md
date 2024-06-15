# 题目
**189. 轮转数组**
> 中等

给定一个整数数组 `nums`，将数组中的元素向右轮转 `k` 个位置，其中 `k` 是非负数。




**示例 1:**

>输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右轮转 1 步: [7,1,2,3,4,5,6]
向右轮转 2 步: [6,7,1,2,3,4,5]
向右轮转 3 步: [5,6,7,1,2,3,4]

**示例 2:**

>输入：nums = [-1,-100,3,99], k = 2
输出：[3,99,-1,-100]
解释: 
向右轮转 1 步: [99,-1,-100,3]
向右轮转 2 步: [3,99,-1,-100]




**提示：**
* `1 <= nums.length <= 105`
* `-231 <= nums[i] <= 231 - 1`
* `0 <= k <= 105`




**进阶：**
* 尽可能想出更多的解决方案，至少有 三种 不同的方法可以解决这个问题。
* 你可以使用空间复杂度为 `O(1)` 的 原地 算法解决这个问题吗？

> Leetcode 地址 https://leetcode.cn/problems/rotate-array/description/



# 思路1

**解法：使用额外的数组**

1. 只要计算出原数组的元素轮转后新的索引即可，此时新索引的计算方式为 `(i + k) % n`
2. 使用一个新的数组 `numsArr` 来存储新索引下的元素值，因为题目要求直接改变参数的 `nums` 数组，而不是返回一个新数组，所以最后还需要把新数组元素的值一个个覆盖掉 `nums` 



**js 代码**

```js
/**
 *
 * @param {number[]} nums
 * @param {number} k
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var rotate = function(nums, k) {
    let n = nums.length;
    let numsArr = new Array(n);
    
    for (let i = 0; i < n; i++) {
        let newIndex = (i + k) % n; // 计算新的索引
        numsArr[newIndex] = nums[i];
    }

    for (let i = 0; i < n; i++) {
        nums[i] = numsArr[i];
    }

    return nums;
};
```



**rust 代码**

```rust
impl Solution {
    pub fn rotate(nums: &mut Vec<i32>, k: i32) {
        let mut nums_arr = nums.clone();

        let n = nums.len();

        for i in 0..n {
            // 计算新索引
            let new_index = (i + k as usize) % n;
            nums_arr[new_index] = nums[i];
        }
 
        for i in 0..n {
            nums[i] = nums_arr[i];
        }
    }

}
```



**复杂度**

* 时间复杂度 `O(n)`，`n` 为 `nums` 数组的长度

* 空间复杂度 `O(n)`



# 思路2

**解法：数组翻转**

将数组的元素向右移动 `k` 次可以理解为：尾部 `k mod n` 个元素会被移动至数组头部，其余元素向右移动 `k mod n` 个位置。

1. 先将所有元素翻转，这样尾部的 `k mod n` 个元素就被移至数组头部
2. 接着再翻转 `[0, (k mod n) − 1))` 区间的元素
3. 最后翻转 `[k mod n, n − 1]` 区间的元素就行

例如以例1的 `nums = [1,2,3,4,5,6,7], k = 3`  为例，`n` 为 7，`k` 为 3

| 操作                                                 | 结果          |
| ---------------------------------------------------- | ------------- |
| 原始数组                                             | 1 2 3 4 5 6 7 |
| 翻转所有元素                                         | 7 6 5 4 3 2 1 |
| 翻转 `[0, k mod n - 1]` 区间的元素，即 [0, 2] 的元素 | 5 6 7 4 3 2 1 |
| 翻转 `[k mod n, n − 1]` 区间的元素，即 [3, 6] 的元素 | 5 6 7 1 2 3 4 |



**js 代码**

```js
/**
 * 
 * @param {number[]} nums
 * @param {number} k
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var rotate = function(nums, k) {
    let n = nums.length;
  
    k = k % n;
  
    // 翻转所有元素
    reverse(nums, 0, n - 1);
    // 翻转 [0, (k % n) − 1)] 区间的元素
    reverse(nums, 0, k - 1);
    // 翻转 [k % n, n − 1] 区间的元素
    reverse(nums, k, n - 1);
};

function reverse(nums, start, end) {
    while (start < end) {
        let tmp = nums[start];
        nums[start] = nums[end];
        nums[end] = tmp;
        start++;
        end--;
    }
}
```



**rust 代码**

```rust
impl Solution {   
    pub fn rotate(nums: &mut Vec<i32>, k: i32) {
        if k == 0 {
            return;
        }
    
        let n = nums.len() as i32;
   
        let new_k = k % n as i32;

        // 翻转所有元素
        reverse(nums, 0, n - 1);
        // 翻转 [0, (k % n) − 1)] 区间的元素
        reverse(nums, 0, new_k - 1);
        // 翻转 [k % n, n − 1] 区间的元素
        reverse(nums, new_k, n - 1);
    }
}

pub fn reverse(nums: &mut Vec<i32>, start: i32, end: i32) {
    if start >= end {
        return;
    }
    let mut i = start as usize;
    let mut j = end as usize;

    while i < j {
        nums.swap(i, j);
        i += 1;
        j -= 1;
    }
}

```



**复杂度**

* 时间复杂度 `O(n)`，`n` 为 `nums` 数组的长度，每个元素被翻转两次，一共 `n` 个元素，因此总时间复杂度为 `O(2n) = O(n)` 

* 空间复杂度 `O(1)`

