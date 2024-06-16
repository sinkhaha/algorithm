# 题目
**448. 找到所有数组中消失的数字**
> 简单

给你一个含 n 个整数的数组 nums ，其中 nums[i] 在区间 [1, n] 内。请你找出所有在 [1, n] 范围内但没有出现在 nums 中的数字，并以数组的形式返回结果。

**示例 1：**
>输入：nums = [4,3,2,7,8,2,3,1]
输出：[5,6]

**示例 2：**
>输入：nums = [1,1]
输出：[2]

**提示：**
* `n == nums.length`
* `1 <= n <= 105`
* `1 <= nums[i] <= n`
**进阶**：你能在不使用额外空间且时间复杂度为 O(n) 的情况下解决这个问题吗? 你可以假定返回的数组不算在额外空间内。 
> leetcode 地址 https://leetcode.cn/problems/find-all-numbers-disappeared-in-an-array/description/

> 解法类似422



# 思路

因为 `nums` 中的数字值的范围是 `1` 到 `n`，这里 `1` 到 `n` 刚好可以映射为 `nums` 数组的下标，可以考虑使用“正负号标记”法。



**正负号标记法：** 下标为 `i` 的元素值的符号，代表着值为 `i + 1` 的元素是否出现过，负号说明出现过，正号说明没出现过



**算法**

1、遍历 `nums` 数组

2、判断元素 `nums[i]` 所映射的"下标为 `nums[i] - 1`  的元素值"在数组中的值是否为负数，即` nums[nums[i] - 1] `

* 如果 `nums[nums[i] - 1]` 是正数，说明 `nums[i]` 当前是第一次出现，此时将 `nums[nums[i] - 1]`  加上负数标记
* 如果 `nums[nums[i] - 1]` 是负数，说明 `nums[i]` 已经出现过一次，此时不用处理

> 注意：因为当 `nums[i]` 出现过一次后会被标记为负数，所以在将 `nums[i]`  作为下标时，需要取绝对值

3、最后再遍历一次 `nums` ，`nums[i]` 为正数，说明 `i + 1` 是数组缺失的数字



**js 代码**

```js
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var findDisappearedNumbers = function(nums) {
    // nums[i]-1做为新下标，把对应元素变为负
    for (let num of nums) {
        let newIndex = Math.abs(num) - 1;
        if (nums[newIndex] > 0) {
            nums[newIndex] = nums[newIndex] * -1;
        }
    }

    // 找到正数的元素，说明nums中没有元素等于此下标
    let result = [];
    for (let i = 0; i< nums.length; i++) {
        // 说明 i+1 是缺失的数字
        if (nums[i] > 0) {
            result.push(i + 1);
        }
    }

    return result;
};
```



**rust 代码**

```rust
impl Solution {
    pub fn find_disappeared_numbers(mut nums: Vec<i32>) -> Vec<i32> {
       // nums[i]-1做为新下标，把对应元素变为负
        for i in 0..nums.len() {
            let new_index = (nums[i].abs() - 1) as usize;
            if nums[new_index] > 0 {
                nums[new_index] = nums[new_index] * -1;
            }
        }

        // 找到正数的元素，说明nums中没有元素等于此下标
        let mut result: Vec<i32> = vec![];
        for i in 0..nums.len() {
            // 说明 i+1 是缺失的数字
            if nums[i] > 0 {
                result.push(i as i32 + 1);
            }
        }

       result
    }
}
```



**复杂度**

 * 时间复杂度：`O(n)`， `n` 为 `nums` 数组的长度

* 空间复杂度：`O(1)`，返回值不计入空间复杂度

