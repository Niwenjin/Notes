# LeetCode

## 目录

[模拟](#模拟)  
[查找](#查找)  
[双指针](#双指针)  
[二分](#二分)  
[滑动窗口](#滑动窗口)  
[树的搜索](#树的搜索)  
[回溯算法](#回溯算法)  
[贪心算法](#贪心算法)  
[递归&迭代](#递归迭代)  
[图论](#图论)  
[动态规划](#动态规划)  
[位运算](#位运算)

## 模拟

### 类约瑟夫问题

#### [390. 消除游戏](https://leetcode.cn/problems/elimination-game/description/)

每次都将整数列表进行间隔删除，因此每次删除后剩余的整数列表都是等差数列。如果 k 是偶数，则从左向右删除；如果 k 是奇数，则从右向左删除。当等差数列只剩一个元素，返回队首元素。

## 查找

## 双指针

## 二分

### 类二分查找

#### [162. 寻找峰值](https://leetcode.cn/problems/find-peak-element/description/)

用类二分查找，若 nums[mid] > nums[mid + 1]，说明峰值在 mid 左边，令 high = mid；若 nums[mid] < nums[mid + 1]，说明峰值在 mid 右边，令 low = mid + 1。循环结束返回 high。

## 滑动窗口

## 树的搜索

## 回溯算法

## 贪心算法

## 递归&迭代

## 图论

### BFS

### DFS

### 多源 BFS

### 双向 BFS

### 迭代加深

### 拓扑排序

### 最短路

### 最小生成树

### 并查集

### 启发式搜索

## 动态规划

### 路径规划

#### [64. 最小路径和](https://leetcode.cn/problems/minimum-path-sum/description/)

建立一个数组储存该位置的最小路径。初始化`dp[0][0] = grid[0]`，遍历数组，每个位置的最小路径更新为`grid[i][j] + MIN(dp[i][j - 1], dp[i - 1][j])`，返回`dp[m-1][n-1]`。

### 类递归问题

#### [70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/description/)

`f(x) = f(x-1) + f(x-2)`类似斐波那契数列。

### 类背包问题

#### [416. 分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/description/)

创建二维数组 dp 包含 n 行 target+1 列，其中`dp[i][j]` 表示从数组的[0,i]下标范围内选取若干个正整数（可以是 0 个），是否存在一种选取方案使得被选取的正整数的和等于 j。遍历更新数组，返回`dp[n-1][target]`。

#### [413. 等差数列划分](https://leetcode.cn/problems/arithmetic-slices/description/)

## 位运算

### 二进制表示

#### [231. 2 的幂](https://leetcode.cn/problems/power-of-two/description/)

一个数 n 是 2 的幂，当且仅当 n 是正整数，并且 n 的二进制表示中仅包含 1 个 1。可以使用两种技巧：

1. `n & (n - 1)`该位运算技巧可以直接将 n 二进制表示的最低位的 1 移除。只需判断`n & (n - 1) = 0`。
2. `n & (-n)`该位运算技巧可以直接获取 n 二进制表示的最低位的 1。只需判断`n & (-n) = n`。
