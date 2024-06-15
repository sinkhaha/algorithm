# 题目
**289. 生命游戏**
> 中等

根据 百度百科 ， 生命游戏 ，简称为 生命 ，是英国数学家约翰·何顿·康威在 1970 年发明的细胞自动机。



给定一个包含 m × n 个格子的面板，每一个格子都可以看成是一个细胞。每个细胞都具有一个初始状态： 1 即为 活细胞 （live），或 0 即为 死细胞 （dead）。每个细胞与其八个相邻位置（水平，垂直，对角线）的细胞都遵循以下四条生存定律：

1. 如果活细胞周围八个位置的活细胞数少于两个，则该位置活细胞死亡；
2. 如果活细胞周围八个位置有两个或三个活细胞，则该位置活细胞仍然存活；
3. 如果活细胞周围八个位置有超过三个活细胞，则该位置活细胞死亡；
4. 如果死细胞周围正好有三个活细胞，则该位置死细胞复活；



下一个状态是通过将上述规则同时应用于当前状态下的每个细胞所形成的，其中细胞的出生和死亡是同时发生的。给你 m x n 网格面板 board 的当前状态，返回下一个状态。



**示例 1：**

![](https://sink-blog-pic.oss-cn-shenzhen.aliyuncs.com/img/leetcode/leetcode1.jpg)

> 输入：board = [[0,1,0],[0,0,1],[1,1,1],[0,0,0]]
输出：[[0,0,0],[1,0,1],[0,1,1],[0,1,0]]



**示例 2：**

![](https://sink-blog-pic.oss-cn-shenzhen.aliyuncs.com/img/leetcode/leetcode2.jpg)

> 输入：board = [[1,1],[1,0]]
输出：[[1,1],[1,1]]



**提示：**
* m == board.length
* n == board[i].length
* 1 <= m, n <= 25
* `board[i][j]` 为 0 或 1



**进阶：**
* 你可以使用原地算法解决本题吗？请注意，面板上所有格子需要同时被更新：你不能先更新某些格子，然后使用它们的更新后的值再更新其他格子。
* 本题中，我们使用二维数组来表示面板。原则上，面板是无限的，但当活细胞侵占了面板边界时会造成问题。你将如何解决这些问题？

> leetcode地址 https://leetcode.cn/problems/game-of-life/description/



# 思路 

**复制原数组**



**js 代码**

```js
/**
 * @param {number[][]} board
 * @return {void} Do not return anything, modify board in-place instead.
 */
var gameOfLife = function(board) {
    // 复制数组
    let copyBoard = board.map(arr => [...arr]); 

    let m = board.length;
    let n = board[0].length;

    for (let row = 0; row < m; row++) {
        for (let col = 0; col < n; col++) {
            // 当前 boards[row][col] 周围8个格的活细胞个数
            let liveCount = 0;

            let neighbors = [-1, 0, 1]; // 向左/不移动/向右 或者 向上/不移动/向下

            for (let nx of neighbors) {
                for (let ny of neighbors) {
                    // 排除当前元素自身
                    if (nx == 0 && ny == 0) {
                        continue;
                    }

                    // 周围元素的下标
                    const newRow = row + nx;
                    const newCol = col + ny;

                    // 边界判断
                    if ((newRow >= 0 && newRow < m) && (newCol >= 0 && newCol < n)){
                        // 活细胞
                        if (copyBoard[newRow][newCol] == 1) {
                            liveCount += 1;
                        }
                    } 
                    
                }
            }

            // 规则1 和 规则3
            if (copyBoard[row][col] == 1 && (liveCount < 2 || liveCount > 3)) {
                board[row][col] = 0;
            }

            // 规则4
            if (copyBoard[row][col] == 0 && liveCount == 3) {
                board[row][col] = 1;
            }
        }
    }
};
```



**rust 代码**

```rust
impl Solution {
  
    pub fn game_of_life(board: &mut Vec<Vec<i32>>) {
        let copy_board = board.clone();

        let m = board.len();
        let n = board[0].len();

        let neighbors: Vec<i32> = vec![-1, 0, 1];

        for row in 0..m {
            for col in 0..n {
                let mut live_count = 0;

                for i in 0..neighbors.len() {
                    for j in 0..neighbors.len() {
                        let nx = neighbors[i as usize];
                        let ny = neighbors[j as usize];

                        // 排除当前元素自身
                        if nx == 0 && ny == 0 {
                            continue;
                        }
                        // 周围元素的下标
                        let new_row = row as i32 + nx;
                        let new_col = col as i32 + ny;

                        // 边界判断
                        if (new_row >= 0 && new_row < m as i32) && (new_col >= 0 && new_col < n as i32)
                    {
                            // 活细胞
                            if copy_board[new_row as usize][new_col as usize] == 1 {
                                live_count += 1;
                            }
                        }
                    }
                }

                // 规则1 和 规则3
                if copy_board[row][col] == 1 && (live_count < 2 || live_count > 3) {
                    board[row][col] = 0;
                }

                // 规则4
                if copy_board[row][col] == 0 && live_count == 3 {
                    board[row][col] = 1;
                }
            }
        }
    }
}
```



**复杂度**

* 时间复杂度：O(mn)，m 和 n 分别为 board 的行数和列数

* 空间复杂度：O(mn)，为复制数组占用的空间



# 优化

**使用额外状态**





**js 代码**

```js
/**
 * @param {number[][]} board
 * @return {void} Do not return anything, modify board in-place instead.
 */
var gameOfLife = function(board) {
    let copyBoard = board.map(arr => [...arr]); 

    let m = board.length;
    let n = board[0].length;

    for (let row = 0; row < m; row++) {
        for (let col = 0; col < n; col++) {
            // 当前 boards[row][col] 周围8个格的活细胞个数
            let liveCount = 0;

            let neighbors = [-1, 0, 1]; // 向左/不移动/向右 或者 向上/不移动/向下

            for (let nx of neighbors) {
                for (let ny of neighbors) {
                    // 排除当前元素自身
                    if (nx == 0 && ny == 0) {
                        continue;
                    }

                    // 周围元素的下标
                    const newRow = row + nx;
                    const newCol = col + ny;

                    // 边界判断
                    if ((newRow >= 0 && newRow < m) && (newCol >= 0 && newCol < n)){
                        // 活细胞,注意: -1 表示原本是活的
                        if (copyBoard[newRow][newCol] == 1 || copyBoard[newRow][newCol] == -1) {
                            liveCount += 1;
                        }
                    } 
                    
                }
            }

            // 规则1 和 规则3
            if (copyBoard[row][col] == 1 && (liveCount < 2 || liveCount > 3)) {
                 // -1 代表这个细胞过去是活的现在死了
                board[row][col] = -1;
            }

            // 规则4
            if (copyBoard[row][col] == 0 && liveCount == 3) {
                // 2 代表这个细胞过去是死的现在活了
                board[row][col] = 2;
            }
 
        }
  
        
        for (let row = 0; row < m; row++) {
            for (let col = 0; col < n; col++) {
               // 1、2 都是活的
                if (board[row][col] > 0) {
                    board[row][col] = 1; 
                } else {
                    // -1、0 都是死的
                    board[row][col] = 0;
                }
            }
        }
 
    }
};
```



**rust 代码**

```rust
impl Solution { 
    pub fn game_of_life(board: &mut Vec<Vec<i32>>) {
        let m = board.len();
        let n = board[0].len();

        let neighbors: Vec<i32> = vec![-1, 0, 1];

        for row in 0..m {
            for col in 0..n {
                let mut live_count = 0;

                for i in 0..neighbors.len() {
                    for j in 0..neighbors.len() {
                        let nx = neighbors[i as usize];
                        let ny = neighbors[j as usize];
                    
                        // 排除当前元素自身
                        if nx == 0 && ny == 0 {
                            continue;
                        }
                    
                        // 周围元素的下标
                        let new_row = row as i32 + nx;
                        let new_col = col as i32 + ny;

                        // 边界判断
                        if (new_row >= 0 && new_row < m as i32) && (new_col >= 0 && new_col < n as i32)
                    {
                            // 活细胞，注意: -1 表示原本是活的
                            if board[new_row as usize][new_col as usize] == 1
                                || board[new_row as usize][new_col as usize] == -1
                            {
                                live_count += 1;
                            }
                        }
                    }
                }

                // 规则1 或 规则3
                if board[row][col] == 1 && (live_count < 2 || live_count > 3) {
                    // -1 代表这个细胞过去是活的现在死了
                    board[row][col] = -1;
                }

                // 规则 4
                if board[row][col] == 0 && live_count == 3 {
                    // 2 代表这个细胞过去是死的现在活了
                    board[row][col] = 2;
                }
            }
        }

        // 遍历 board 得到一次更新后的状态
        for row in 0..m {
            for col in 0..n {
                if board[row][col] > 0 {
                    // 1、2 都是活的
                    board[row][col] = 1;
                } else {
                    // -1、0 都是死的
                    board[row][col] = 0;
                }
            }
        }
    }
  
}
```



**复杂度**

* 时间复杂度：O(mn)，m 和 n 分别为 board 的行数和列数

* 空间复杂度：O(1)，除原数组外只需要常数的空间存放若干变量

