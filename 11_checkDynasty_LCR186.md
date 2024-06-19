# 题目
**LCR 186. 文物朝代判断**
> 简单

展览馆展出来自 13 个朝代的文物，每排展柜展出 5 个文物。某排文物的摆放情况记录于数组 places，其中 places[i] 表示处于第 i 位文物的所属朝代编号。其中，编号为 0 的朝代表示未知朝代。请判断并返回这排文物的所属朝代编号是否连续（如遇未知朝代可算作连续情况）。



**示例 1：**
>输入: places = [0, 6, 9, 0, 7]
>
>输出: True

**示例 2：**
>输入: places = [7, 8, 9, 10, 11]
>
>输出: True

**提示：**

* `places.length = 5`
* `0 <= places[i] <= 13`

> leetcode地址https://leetcode.cn/problems/bu-ke-pai-zhong-de-shun-zi-lcof/description/



# 思路

**分析如下**

1、当没有出现“未知朝代”的情况

（1）如果朝代重复了，则一定不连续

（2）假设 `5` 个朝代中，最大的朝代为 `max`，最小的朝代为 `min`，则要满足 `max - min < 5` 时，这 `5` 个朝代才是连续的



2、当出现“未知朝代”的情况

（1）当出现 `1` 个未知朝代时，因为未知朝代可以代表任何朝代，那其余 `4` 个朝代满足 `max - min < 5` 时，这5个朝代也会连续，当 `max - min >= 5` 时，就不连续 ，例如

* 有 `0 2 3 4 6` 等 `5` 个朝代，此时当"未知朝代"为1或为5时都是连续的，此时 `6 - 2 < 5 `

* 有 `0 2 3 4 7` 等  `5` 个朝代，此时不管"未知朝代"为什么，都是不连续的，此时 `7 - 2 >= 5`

（2）当出现 `2` 个未知朝代或 `3` 个未知朝代，也一样是满足 `max - min < 5`时，朝代是连续的

（3）当出现 `4` 个 或 `5` 个未知朝代时，一定是连续的，也一样满足 `max - min < 5`



注意：同一个朝代的文物是可以重复选择的



**算法流程**

1、遍历 `places` 数组，用一个 `Set` 存储出现过的朝代（不用存储未知朝代）

2、当出现未知朝代时，直接跳过；当出现过重复朝代时，说明一定不连续，直接返回

3、遍历过程中，计算 `places` 数组的最大值 `max` 和 最小值 `min`（未知朝代除外）

4、最后返回 `max - min < 5` 即可



**rust 代码**

```rust
impl Solution {
    pub fn check_dynasty(places: Vec<i32>) -> bool {
        use std::collections::HashSet;
        
        // 存放出现过的朝代，不包括未知朝代
        let mut place_list: HashSet<i32> = HashSet::new();

        // 存放朝代的最小值，因为朝代最大13，所以初始化14表示相对大
        let mut min = 14;
        // 存放朝代的最大值，因为朝代最小0，所以初始化-1，表示相对小
        let mut max = -1;

        for &place in &places {
            // 未知朝代可以跳过
            if place == 0 {
                continue;
            }
            // 出现重复朝代，肯定不是连续的
            if place_list.contains(&place) {
                return false;
            }

            place_list.insert(place);
            
            // 计算朝代最大值
            max = place.max(max);
            // 计算朝代最小值
            min = place.min(min);
        }

        max - min < 5
    }
}
```





**js 代码**

```js
/**
 * @param {number[]} places
 * @return {boolean}
 */
var checkDynasty = function(places) {
    // 存放出现过的朝代，不包括未知朝代
    let set = new Set(); 

    // 存放朝代的最小值，因为朝代最大13，所以初始化14表示相对大
    let min = 14;
    // 存放朝代的最大值，因为朝代最小0，所以初始化-1，表示相对小
    let max = -1;

    for (let place of places) {
        // 未知朝代可以跳过
        if (place == 0) {
            continue;
        }
        // 出现重复朝代，肯定不是连续的
        if (set.has(place)) {
            return false;
        }
        set.add(place);
        
        // 计算朝代最大值
        min = Math.min(place, min);
        // 计算朝代最小值
        max = Math.max(place, max);
    }

    return max - min < 5;
};
```



**复杂度**

* 时间复杂度 `O(1)`，因为只能存放 `5` 个朝代的文物，时间复杂度是固定的
* 空间复杂度 `O(1)`，虽然用了 `Set` 存储，但是文物的个数固定是 `5`，所以是固定的空间复杂度

