# 题目

**768. 最多能完成排序的块 II**

>困难

这个问题和“最多能完成排序的块”相似，但给定数组中的元素可以重复，输入数组最大长度为2000，其中的元素最大为10**8。

arr是一个可能包含`重复元素`的整数数组，我们将这个数组分割成几个“块”，并将这些块分别进行排序。之后再连接起来，使得连接的结果和按升序排序后的原数组相同。

我们最多能将数组分成多少块？



**示例 1:**

```
输入: arr = [5,4,3,2,1]
输出: 1
解释:
将数组分成2块或者更多块，都无法得到所需的结果。
例如，分成 [5, 4], [3, 2, 1] 的结果是 [4, 5, 1, 2, 3]，这不是有序的数组。
```

**示例 2:**

```
输入: arr = [2,1,3,4,4]
输出: 4
解释:
我们可以把它分成两块，例如 [2, 1], [3, 4, 4]。
然而，分成 [2, 1], [3], [4], [4] 可以得到最多的块数。 
```



**提示:**

* arr的长度在[1, 2000]之间。
* arr[i]的大小在[0, 10^8]之间。

>leetcode地址：https://leetcode-cn.com/problems/max-chunks-to-make-sorted-ii



# 解法1: 单调递增栈

## 思路

因为题目要求的是`最多的块`，最多的块的情况下只能是每一个元素都可以当成一个块，此时`数组是递增`或者是`递减`的。



**单调递增栈**

* 维护单调栈，单调栈中每个元素代表每个块中的最大值；所求结果即为：栈元素的个数（栈有多少个元素就有多少个块）



**算法如下**

1、从左往右遍历数组 `arr`

2、如果当前值 `arr[i] >= 单调递增栈的栈顶元素` ，`arr[i]`直接入栈（表示当前元素 `arr[i]` 可以算为一个单独的块，因为要求的是最多的块）

3、如果当前值 `arr[i] < 单调递增栈的栈顶元素` ，说明 `arr[i]` 不可以单独作为一个块，必须往前面找到比它小的栈顶元素为止，表示当前值 `arr[i]` 可以和前面这些元素组成一个块

* 第一个栈顶元素先出栈，保存为 `max`（表示当前块的最大值是 `max`）
* 循环继续比较 `arr[i]` 和下一个栈顶元素的大小，一直循环到当前值 ` arr[i] >= 下一个栈顶元素` 停止，说明当前元值 `arr[i]` 和这些出栈的所有元素可以组成一个块，这个块的最大值是最开始的栈顶元素 `max`
* 最后把当前块的最大值 `max` 放入栈即可



## 代码

**rust 代码**

```rust
impl Solution {
    pub fn max_chunks_to_sorted(arr: Vec<i32>) -> i32 {
        let mut stack: Vec<i32> = Vec::new();

        for i in 0..arr.len() {
            let val = arr[i];

            // 大于栈顶元素，直接入栈，算一个块
            if stack.len() == 0 || val >= stack[stack.len() - 1] {
                stack.push(val);
            } else {
                let cur_max = stack.pop().unwrap();

                // val和这些弹出的元素算一个块
                while stack.len() > 0 && val < stack[stack.len() - 1] {
                    stack.pop().unwrap();
                }

                stack.push(cur_max);
            }
        }

        stack.len() as i32
    }
}
```



**js 代码**

```javascript
/**
 * @param {number[]} arr
 * @return {number}
 */
var maxChunksToSorted = function(arr) {
    let stack = [];

    for (let val of arr) {
        // 大于栈顶元素，直接入栈，算一个块
        if (!stack.length || val >= stack[stack.length - 1]) {
            stack.push(val);
        } else {
            let curMax = stack.pop();

            // val和这些弹出的元素算一个块
            while (stack.length && val < stack[stack.length - 1]) {
                stack.pop();
            }
            
            // 保存当前块中的最大值
            stack.push(curMax);
        }
    }

    return stack.length;
};
```



## 复杂度

 * 时间复杂度 `O(n)`

 * 空间复杂度 `O(n)`

   

# 解法2: 排序和

## 思路

**排序和解法**

如果一个数组 `arr` 和该数组经过排序后的值 `sortArr` 的前 `i` 项和相等时，则说明两个数组中`[0...i]` 这些元素都是相同的（可能就只有顺序不同）

 

**算法如下**

1. 对 `arr` 进行升序排序，排序后数组为 `sortArr`
2. 从左往右遍历 `arr`，同时计算当前 `i` ，`arr` 的前 `i` 项和 ，和  `sortArr` 的前 `i` 项和
3. 如果两个数组的前 `i` 项和相等，即 `arr 的前 i 项和 == sortArr 的前 i 项和`，说明这前 `i` 项可以组成一个块，增加结果计数；如果不相等，则继续遍历



## 代码

**rust 代码**

```rust
impl Solution {
    pub fn max_chunks_to_sorted(arr: Vec<i32>) -> i32 {
        let mut count = 0;

        let mut sum1 = 0; // arr 前 n 项和
        let mut sum2 = 0;  // arr_sort 前 n项和

        // 拷贝数组，升序排序
        let mut arr_sort = arr.clone();
        arr_sort.sort();

        for i in 0..arr.len() {
            sum1 += arr[i];
            sum2 += arr_sort[i];

            // 此时可以组成一个块
            if (sum1 == sum2) {
                count += 1;
            }
        }

        count
    }
}
```



**js 代码**

```javascript
/**
 * @param {number[]} arr
 * @return {number}
 */
var maxChunksToSorted = function(arr) {
    let count = 0;
    
    // 拷贝数组，升序排序
    let arrSort = [...arr].sort((a, b) => a - b);

    // arr 前 n 项和
    let sum1 = 0;
    // arrSort 前 n项和
    let sum2 = 0;

    for (let i = 0; i < arr.length; i++) {
        sum1 += arr[i];
        sum2 += arrSort[i];

        // 此时可以组成一个块
        if (sum1 == sum2) {
            count++;
        }
    }

    return count;
};
```

## 复杂度

* 时间复杂度 `O(nlogn)`，排序 `sort` 的时间复杂度 `nlogn`， `for` 循环的时间复杂度 `n`，`Max(nlogn, n ) 为 n`

 * 空间复杂度 `O(n)`



## 其他

**相似的解法还有计数排序解法**：如果两个元素一样的数组，不管元素顺序怎样，两个数组的计数排序数组一定是一样的（计算排序数组即索引值为元素，值为在原数组出现的个数）

1. 对 `arr `进行升序排序，得到升序排序后的数组 `sortArr`
2. 从左往右遍历 `arr` ，如果 `arr` 的前 `i` 项的计数信息和 `sortArr` 的前 `i` 项的计数信息一致，那么可以分成一个块，此时分块计数加 1，否则继续遍历

