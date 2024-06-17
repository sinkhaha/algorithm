# 题目
**621. 任务调度器**
> 中等

给你一个用字符数组 tasks 表示的 CPU 需要执行的任务列表，用字母 A 到 Z 表示，以及一个冷却时间 n。每个周期或时间间隔允许完成一项任务。任务可以按任何顺序完成，但有一个限制：两个 相同种类 的任务之间必须有长度为 n 的冷却时间。



返回完成所有任务所需要的 最短时间间隔 。



**示例 1：**

>输入：tasks = ["A","A","A","B","B","B"], n = 2
>
> 输出：8
> 
> 解释：A -> B -> (待命) -> A -> B -> (待命) -> A -> B
     在本示例中，两个相同类型任务之间必须间隔长度为 n = 2 的冷却时间，而执行一个任务只需要一个单位时间，所以中间出现了（待命）状态。 

**示例 2：**

>输入：tasks = ["A","A","A","B","B","B"], n = 0
>
> 输出：6
> 
> 解释：在这种情况下，任何大小为 6 的排列都可以满足要求，因为 n = 0
["A","A","A","B","B","B"]
["A","B","A","B","A","B"]
["B","B","B","A","A","A"]
...
诸如此类

**示例 3：**

>输入：tasks = ["A","A","A","A","A","A","B","C","D","E","F","G"], n = 2
>
> 输出：16
>
> 解释：一种可能的解决方案是：
     A -> B -> C -> A -> D -> E -> A -> F -> G -> A -> (待命) -> (待命) -> A -> (待命) -> (待命) -> A

**提示：**
* `1 <= tasks.length <= 104`
* `tasks[i] 是大写英文字母`
* `0 <= n <= 100`

> leetcode 地址 https://leetcode.cn/problems/task-scheduler/description/



# 思路

**先理解一下题意**

题目说明了有一批任务，如果是同一种类的任务，那它们之间必须相隔 n 个单位的冷却时间，执行一个任务需要一个单位时间，计算完成所有任务需要的最短时间。



以 `tasks = ["A","A","A","B","B","B"], n = 2` 为例：

1、其中有 3 个 A 任务，有 3 个 B 任务，A 和 A 之间是同一类任务，B 和 B之间是同一类任务，所以 A 任务执行后必须隔 2 个冷却时间才可以继续执行 A 任务（B 也类似）

2、假设此时先执行 A 任务，因为 A 和 A 之间相隔 2 个单位的冷却时间，所以必须是这样的顺序 **`A -> (1个单位的冷却时间) -> (1个单位的冷却时间) -> A  -> (1个单位的冷却时间) -> (1个单位的冷却时间) -> A`** ，然而在冷却时间内也可以执行其他任务，假设在冷却时间执行B任务，则最终的执行顺序为 **`A -> B -> (1个单位的冷却时间) -> A  -> B -> (1个单位的冷却时间) -> A -> B `** ，所以最终最短的执行时间是 8 



## 解法: 构造法

核心：完成所有任务的最短时间取决于**出现次数最多的任务的数量**



分析如下：

1、假设所有任务种类中出现次数最多的一种是 `A`，记为 `maxCount`



2、如下图，使用一个宽为 `n + 1` 的矩阵表示执行完所有 `A` 所花的时间（从左往右，从上往下执行），每两个 `A` 之间相隔 `n` 个单位的冷却时间。空白的格子表示 `CPU` 待命的状态，待命状态 `CPU` 也可以执行其他任务。从图中可以看出，此时完成所有任务的最短时间即为所有表格的格子数，即

`（maxCount - 1）* (n + 1) + 1` 



![](https://sink-blog-pic.oss-cn-shenzhen.aliyuncs.com/img/leetcode20240617234226.png)



3、当然，出现次数为 `maxCount`  次的不一定只有 `A` 任务这一种任务，可能多种任务出现的次数都是 `maxCount` 次，例如有 `B` 任务，`C` 任务也是  `maxCount`  次 ，此时需要将它们排布在矩阵的第二、三列，如下图

![](https://sink-blog-pic.oss-cn-shenzhen.aliyuncs.com/img/leetcode20240617234247.png)

此时最短完成时间为 `（maxCount - 1）* (n + 1) + 3` ，用  `maxSameCount` 表示次数等于 `maxCount` 的任务种类数，此时 `maxSameCount` 等于 3，可以得到最短的完成时间为

`（maxCount - 1）* (n + 1) + maxSameCount` 

> 有一种情况例外，当 `maxSameCount > n + 1`，说明多出的任务无法排进矩阵的某一列中，这种情况后面再讲



4、当处理完了所有执行次数为 `maxCount` 次的各种任务，剩余的任务的执行次数一定小于 `maxCount`，此时一定可以把它们放到上面的矩阵中。放置方法为：从倒数第二行开始，先放入靠左侧的列，同一列中从下往上依次放入每一种任务，并且同一种任务需要连续的放入。例如还有任务 `D`，`E` 和 `F` 时，会按照下图的方式依次放入这些任务

![](https://sink-blog-pic.oss-cn-shenzhen.aliyuncs.com/img/leetcode20240617234259.png)

由图可知，任意一种任务，一定不会被放入同一行两次，且任意两个相邻的任务之间要么间隔 `n`（例如上图中位于同一列的相同任务），要么间隔 `n+1`（例如上图中第一列的 `F` 和第二列的 `F`）。放置完后，可以保证总时间仍然为：

`（maxCount - 1）* (n + 1) + maxSameCount` 



5、如果 `maxSameCount > n + 1` ，则不符合用上面的方式，此时说明实际这个矩阵超出了 `n + 1` 列，那么就没有 `CPU` 的待命的状态了（即冷却时间都有其他任务要执行），任意两个相邻任务的执行间隔肯定也会至少为 `n`，此时总执行时间就是任务的总数 `taskCount`



**小结：**

1. 任务执行过程中存在待命状态（有空余的冷却时间），此时最短完成时间为

`（maxCount - 1）* (n + 1) + maxSameCount` 

2. 任务执行过程中不存在待命状态（没有空余的冷却时间，冷却时间都在执行其他任务），此时最短完成时间为任务总数，即 `taskCount`

因此，在任意的情况下，需要的最少时间就是 `（maxCount - 1）* (n + 1) + maxSameCount`  和 `taskCount` 中的较大值



## 代码

**js 代码**

```js
/**
 * @param {character[]} tasks
 * @param {number} n
 * @return {number}
 */
var leastInterval = function(tasks, n) {
    let taskCountMap = {}; // key为任务，value为出现次数

    // 计算每个任务的次数
    for (let task of tasks) {
        if (taskCountMap[task]) {
            taskCountMap[task] = taskCountMap[task] + 1;
        } else {
            taskCountMap[task] = 1;
        }
    }
    
    // 任务出现最多的次数
    let maxCount = Math.max(...Object.values(taskCountMap));

    // ”任务出现最多的且相等“的任务种类数
    let maxSameCount = 0;

    Object.values(taskCountMap).forEach(count => {
        if (count == maxCount) {
            maxSameCount++;
        }
    });
    
    const time = (maxCount - 1) * (n + 1) + maxSameCount;

    return Math.max(time, tasks.length);
};
```



**rust 代码**

```rust
impl Solution {
   pub fn least_interval(tasks: Vec<char>, n: i32) -> i32 {
       // key为任务，value为出现次数
       let mut task_count_map = std::collections::HashMap::new();

        // 任务出现最多的次数
        let mut max_count = 0;

        for task in tasks.iter() {
            let count = task_count_map.entry(task).or_insert(0);
            *count += 1;

            max_count = max_count.max(*count);
        }

        // ”任务出现最多的且相等“的任务种类数
        let mut max_same_count = 0;
        for &count in task_count_map.values() {
            if count == max_count {
                max_same_count += 1;
            }
        }

        let time = (max_count - 1) * (n + 1) + max_same_count;

        (tasks.len() as i32).max(time) as i32
    }
}
```



## 复杂度

* 时间复杂度：`O(n)`，`n` 为 `tasks` 任务的个数

* 空间复杂度：`O(1)`，因为任务都是大写字母表示，最多 26 个字母，每个任务计算次数的存储空间也是固定的



**参考**

* [官方题解](https://leetcode.cn/problems/task-scheduler/solutions/509687/ren-wu-diao-du-qi-by-leetcode-solution-ur9w/)

