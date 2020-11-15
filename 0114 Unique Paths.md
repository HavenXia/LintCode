# 题目地址

https://www.lintcode.com/problem/unique-paths/

# 题目描述

A robot is located at the top-left corner of a *m* x *n* grid.

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid.

How many possible unique paths are there?

### Example

**Example 1:**

```
Input: n = 1, m = 3
Output: 1	
Explanation: Only one path to target position.
```

**Example 2:**

```
Input:  n = 3, m = 3
Output: 6	
Explanation:
	D : Down
	R : Right
	1) DDRR
	2) DRDR
	3) DRRD
	4) RRDD
	5) RDRD
	6) RDDR
```

### Notice

*m* and *n* will be at most 100.
The answer is guaranteed to be in the range of 32-bit integers

Input test data (one parameter per line)How to understand a testcase?



# 思路

直接从combination计算的话, 应该是$\binom{n+m}{n} = \frac{(n+m)!}{n!m!}$

这里应该用dp做, 构造一个mxn的dp

### 方法一 自底向上的多重循环

+ State: dp 记录从当前点到右下角点的path数量
+ Initialize:
  + 最右下角的path数量为1
  + 最底层path数量都为1, dp[m-1] [0] 到 dp[m-1] [n-1] 都是1, **最底层只能往右走**
  + 最右列path数量都为1, dp[0] [n-1] 到dp[m-1] [n-1] 都是1, **最右列只能向下走**

+ Function:  使用==双重for循环==, i从m-2到0, j从n-2到0, **`dp[i][j] = dp[i+1][j] + dp[i][j+1]`**
+ Answer:  返回的是`dp[0][0]`

```java
public int uniquePaths(int m, int n) {
    // State
    int[][] dp = new int[m][n];
    // Initialize 
    dp[m - 1][n - 1] = 0;
    for (int i = 0; i < m; i++) {
        dp[i][n - 1] = 1;
    }
    for (int i = 0; i < n; i++) {
        dp[m - 1][i] = 1;
    }
    // Function 
    for (int i = m - 2; i >= 0; i--) {
        for (int j = n - 2; j >= 0; j--) {
            dp[i][j] = dp[i + 1][j] + dp[i][j + 1];
        }
    }
    return dp[0][0];
}
```

时间复杂度为O(m+n) ,空间复杂度相同



### 方法二 自顶向下的多重循环

+ State: dp 记录从0,0 到当前点的path数量
+ Initialize:
  + 最左上角的path为1
  + 最上层path数量都为1, dp[0] [0] 到 dp[0] [n-1] 都是1, **只能从0,0向右走**
  + 最左列path数量都为1, dp[0] [0] 到dp[m-1] [0] 都是1, **只能从0,0向下走**

+ Function:  使用==双重for循环==, i从1到m-1, j从0到n-1, **`dp[i][j] = dp[i-1][j] + dp[i][j-1]`**
+ Answer:  返回的是`dp[m-1][n-1]`

```java
public int uniquePaths(int m, int n) {
    // State
    int[][] dp = new int[m][n];
    // Initialize 
    dp[0][0] = 1;
    for (int i = 0; i < m; i++) {
        dp[i][0] = 1;
    }
    for (int i = 0; i < n; i++) {
        dp[0][i] = 1;
    }
    // Function 
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
        }
    }
    return dp[m - 1][n - 1];
}
```

