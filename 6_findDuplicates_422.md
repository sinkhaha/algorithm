# 题目
**442. 数组中重复的数据**

> 中等

给你一个长度为 n 的整数数组 nums ，其中 nums 的所有整数都在范围 [1, n] 内，且每个整数出现 一次 或 两次 。请你找出所有出现 两次 的整数，并以数组形式返回。



你必须设计并实现一个时间复杂度为 O(n) 且仅使用常量额外空间的算法解决此问题。




**示例 1：**
>输入：nums = [4,3,2,7,8,2,3,1]
输出：[2,3]

**示例 2：**
>输入：nums = [1,1,2]
输出：[1]

**示例 3：**
>输入：nums = [1]
输出：[]

**提示：**
* `n == nums.length`
* `1 <= n <= 105`
* `1 <= nums[i] <= n`
* `nums` 中的每个元素出现 一次 或 两次

> leetcode 地址 https://leetcode.cn/problems/find-all-duplicates-in-an-array/description/

>  解法有点类似448



# 思路

因为题目要求了使用常量额外空间实现，即空间复杂度为 `O(1)` ，说明不能使用额外的空间，所以不能复制 `nums` 数组。又因为 `nums` 中的数字值的范围是 `1` 到 `n`，这里 `1` 到 `n` 刚好可以映射为 `nums` 数组的下标，且题目规定元素最多出现次数是 2，因此可以考虑使用“正负号标记”法。



**正负号标记法：**下标为 `i` 的元素值的符号，代表着值为 `i + 1` 的元素是否出现过，负号说明出现过，正号说明没出现过



**算法**

1、遍历 `nums` 数组

2、判断元素 `nums[i]` 所映射的"下标为 `nums[i] - 1`  的元素值"在数组中的值是否为负数，即` nums[nums[i] - 1] `

* 如果 `nums[nums[i] - 1]` 是正数，说明 `nums[i]` 当前是第一次出现，此时将 `nums[nums[i] - 1]`  加上负数标记
* 如果 `nums[nums[i] - 1]` 是负数，说明 `nums[i]` 已经出现过一次，当前是第二次出现，此时 `nums[i - 1] ` 就是我们要的值（出现过两次的元素）

> 注意：因为当 `nums[i]` 出现过一次后会被标记为负数，所以在将 `nums[i]`  作为下标时，需要取绝对值



**rust 代码**

```rust
impl Solution {
    pub fn find_duplicates(mut nums: Vec<i32>) -> Vec<i32> {
        let mut result: Vec<i32> = vec![];
   
        for i in 0..nums.len() {
            // 因为重复时，可能已经被设置为负数了，下标需要取绝对值
            let value = nums[i].abs();

            // 减1即转换为下标
            let new_index = (value - 1) as usize;

            // 说明已经出现过一次了，现在就是第2次出现了
            if nums[new_index] < 0 {
                result.push(value); // 元素的绝对值即重复的数字
            } else {
                // 下标对应的值转为负数
                nums[new_index] = -1 * nums[new_index];
            }
        }

        result
    }

}
```



**js 代码**

```js
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var findDuplicates = function(nums) {
    let result = [];

    for (let num of nums) {
        // 因为重复时，可能已经被设置为负数了，下标需要取绝对值
        const value = Math.abs(num);

        // 减1即转换为下标
        const newIndex = value - 1;
        
        // 说明已经出现过一次了，现在就是第2次出现了
        if (nums[newIndex] < 0) {
            result.push(value); // 元素的绝对值即重复的数字
        } else {
            // 下标对应的值转为负数
            nums[newIndex] = -1 * nums[newIndex];
        }
    }

    return result;
};
```



**复杂度**

* 时间复杂度：`O(n)`， `n` 为 `nums` 数组的长度

* 空间复杂度：`O(1)`，返回值不计入空间复杂度

