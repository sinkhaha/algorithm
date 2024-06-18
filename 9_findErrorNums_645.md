# 题目
**645. 错误的集合**
> 简单

集合 s 包含从 1 到 n 的整数。不幸的是，因为数据错误，导致集合里面某一个数字复制了成了集合里面的另外一个数字的值，导致集合 丢失了一个数字 并且 有一个数字重复 。



给定一个数组 nums 代表了集合 S 发生错误后的结果。



请你找出重复出现的整数，再找到丢失的整数，将它们以数组的形式返回。



**示例 1：**
>输入：nums = [1,2,2,4]
>
>输出：[2,3]

**示例 2：**
>输入：nums = [1,1]
>
>输出：[1,2]

提示：
* `2 <= nums.length <= 10^4`
* `1 <= nums[i] <= 10^4`

> leetcode 地址 https://leetcode.cn/problems/set-mismatch/description/



# 思路

## 解法：哈希表法

1、遍历数组，使用哈希表记录每个数字出现的次数，键是数字，值是数字出现的次数

> 重复的数字在数组中出现 2 次，丢失的数字在数组中出现 0 次

2、接着遍历从 1 到 n 的每个数字，从哈希表中找到出现 2 次和出现 0 次的数字即可



**js 代码**

```js
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var findErrorNums = function(nums) {
    let result = new Array(2).fill(0);
    
    const n = nums.length;

    // key是数字，value是出现次数
    const map = new Map();
    for (const num of nums) {
        map.set(num, (map.get(num) || 0) + 1);
    }

    for (let i = 1; i <= n; i++) {
        const count = map.get(i) || 0;
        if (count === 2) {
            result[0] = i; // 注意返回结果第一位是重复的数字
        } else if (count === 0) {
            result[1] = i;
        }
    }

    return result;
};
```



**复杂度**

* 时间复杂度 `O(n)`
* 空间复杂度 `O(n)`



## 解法：正负号标记法

> 解法类似 442 题

上面哈希表法用了 `O(n)`  的空间复杂度，可以进一步优化为 `O(1)`  的时间复杂度。



因为 `nums` 中的数字值的范围是 `1` 到 `n`，这里 `1` 到 `n` 刚好可以映射为 `nums` 数组的下标，且只有一个重复数字，所以元素最多出现次数是 `2`。



**正负号标记法：** 下标为 `i` 的元素值的符号，代表着值为 `i + 1` 的元素是否出现过，负号说明出现过，正号说明没出现过



**正负标记法的算法如下**

1、遍历 `nums` 数组

2、判断元素 `nums[i]` 所映射的"下标为 `nums[i] - 1` 的元素值"在数组中的值是否为负数，即 `nums[nums[i] - 1]` 是否为负数

- 如果 `nums[nums[i] - 1]` 是正数，说明 `nums[i]` 当前是第一次出现，此时将 `nums[nums[i] - 1]` 加上负数标记
- 如果 `nums[nums[i] - 1]` 是负数，说明 `nums[i]` 已经出现过一次，当前是第二次出现，此时 `nums[i - 1] ` 就是重复的数字

> 注意：因为当 `nums[i]` 出现过一次后会被标记为负数，所以在将 `nums[i]` 作为下标时，需要取绝对值

3、最后再找出缺失的数字，遍历 `nums` 数组，判断 `nums[i]` 是否为正数，正数说明数组没有出现过值为 `i + 1` 的数字，所以  `i + 1` 就是缺失的数字



**js 代码**

```js
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var findErrorNums = function(nums) {
    let result = [];

    for (let i = 0; i < nums.length; i++) {
        let newIndex = Math.abs(nums[i]) - 1;
        if (nums[newIndex] > 0) {
            nums[newIndex] = nums[newIndex] * -1;
        } else {
            result.push(Math.abs(nums[i]));
        }
    }

    for (let i = 0; i < nums.length; i++) {
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
    pub fn find_error_nums(mut nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();

        let mut result: Vec<i32> = vec![];

        for i in 0..n {
            let new_index = (nums[i].abs() - 1) as usize;
            if nums[new_index] > 0 {
                nums[new_index] = nums[new_index] * -1;
            } else {
                result.push(nums[i].abs());
            }
        }

        for i in 0..n {
            if nums[i] > 0 {
                result.push(i as i32 + 1);
            }
        }

        result
    }
}
```



**复杂度**

* 时间复杂度 `O(n)`
* 空间复杂度 `O(1)`

