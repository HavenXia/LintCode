# 题目地址

https://www.lintcode.com/problem/triangle



# 题目描述

Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

### Example

**Example 1:**

```
Input the following triangle:
[
[2],
[3,4],
[6,5,7],
[4,1,8,3]
]
Output: 11
Explanation: The minimum path sum from top to bottom is 11 (i.e., 2 + 3 + 5 + 1 = 11).
```

**Example 2:**

```
Input the following triangle:
[
[2],
[3,2],
[6,5,7],
[4,4,8,1]
]
Output: 12
Explanation: The minimum path sum from top to bottom is 12 (i.e., 2 + 2 + 7 + 1 = 12).
```

### Notice

Bonus point if you are able to do this using only O(n) extra space, where n is the total number of rows in the triangle.



# 思路

首先要注意, 这个不是二叉树! 数字三角形的node是可以有两个父节点的

需要找到的是minSum, 即minimum path sum

数据按照二维矩阵给出, 可以理解为正直角三角形, 每一个点(x,y) 分别走向(x+1,y)和(x+1,y+1), <font color = green>**这里的xy不是坐标, 是第几行第几个**</font> 

**数字三角形和二叉树的区别**

同样是n层, 数字三角形一共有n(n+1)/2(**O(n<sup>2</sup>)**)个节点, 满二叉树有**2<sup>n</sup>-1(O(2<sup>n</sup>))**个节点, 二叉树比较多

### 解法一 DFS遍历法

用DFS来做, 用一个traverse函数传一个sum的值

注意此处需要同时传sum和minSum, 但是为了防止minSum的回溯, 用一个int[] 来存它

```java
public int minimumTotal(int[][] triangle) {
    if (triangle == null || triangle.length == 0|| triangle[0].length == 0) {
        return 0;
    }
    int[] minSum = new int[]{Integer.MAX_VALUE};
    traverse(triangle, 0, 0, 0, minSum);
    return minSum[0];
}

private void traverse(int[][] triangle, int x, int y, int sum, int[] minSum) {

    if (x == triangle.length) {
        minSum[0] = Math.min(minSum[0], sum);
        return;
    }
    traverse(triangle, x + 1, y, sum + triangle[x][y], minSum);
    traverse(triangle, x + 1, y + 1, sum + triangle[x][y], minSum);
}
```

**但是失败了,因为这样的还是会超时, 因为这样做的时间复杂度不是O(n<sup>2</sup>), 而是O(2<sup>n</sup>)(<font color = green>n是层数</font>), 有一些点被走了很多遍! 造成了时间的多余**



### 解法二 DFS分治法

用分治法来做, return value就是当前节点走到最底层的minSum

不同于[Minimum Subtree](/Users/parallax/Documents/求职/LintCode/0596 Minimum Subtree.md), 此处必须从当前节点走, 所以只要取左右的较小值加上当前节点值即可

```java
public int minimumTotal(int[][] triangle) {
    if (triangle == null || triangle.length == 0|| triangle[0].length == 0) {
        return 0;
    }
    return divideConquer(triangle, 0, 0);
}

private int divideConquer(int[][] triangle, int x, int y) {
    if (x == triangle.length) {
        return 0;
    }
    int left = divideConquer(triangle, x + 1, y);
    int right = divideConquer(triangle, x + 1, y + 1);
    return Math.min(left, right) + triangle[x][y];
}
```

**但是同样超时了, 因为这样做的时间复杂度不是O(n<sup>2</sup>), 而是O(2<sup>n</sup>)(<font color = green>n是层数</font>), 每个点被上方两个点调用, <font color = red>但是上方的点又被更上方的点调用</font>! 造成了时间的多余**

因为这里不是binary tree, left和right的遍历中也会出现多次重复!



### 解法三 记忆化搜索 Memoization Search

从分治法出发, 遍历法看不出来, 但是从分治法可以看出, divideConquer 出现了重复调用, 比如triangle[2] [1] 在**triangle[1] [0] 和triangle [1] [1]的后续都被调用了!**

**所以需要能够跳过第二次调用, 怎么实现?**

==Hash Table==, 用一个HashMap来存储(x,y) 对应的值, 第一次走到 divideConquer(triangle, x, y) 之后就把从这个节点出发的minSum直接存下来

第二次走到的时候检测一下, 是否存在, 存在就直接return map.get() 即可获得

此处的key可以用"x,y" 的string来表示, HashMap会对string用equals, 所以可以用来查询(**只能在数字三角形里这么做**)

```java
public int minimumTotal(int[][] triangle) {
    if (triangle == null || triangle.length == 0 || triangle[0] == null || triangle[0].length == 0) {
        return 0;
    }
    Map <String, Integer> map = new HashMap<>();
    return memorySearch(triangle, 0, 0, map);
}

private int memorySearch(int[][] triangle, int x, int y,Map<String, Integer> map) {
    if (x == triangle.length) {
        return 0;
    }
    String key = Integer.toString(x) + "," + Integer.toString(y);
    if (map.containsKey(key)) {
        return map.get(key);
    }
    int left = memorySearch(triangle, x + 1, y, map);
    int right = memorySearch(triangle, x + 1, y + 1, map);
    map.put(key, Math.min(left, right) + triangle[x][y]);
    return map.get(key);
}
```

这样的话**时间复杂度可以降到O(n<sup>2</sup>)**, 所有的点只调用两次, 第一次走到底存下值, 第二次直接从map里取

**<font color = red>注意: 此时就算上方的点也有两次调用, 也不会再用到xy了,而是直接在map里获取上方节点值!</font>**

但是java的map存起来真的好蠢啊!



### 解法四 多重循环(自底向上)

需要return的value是最小路径总和的值

可以从最底层往上走, 每个node的返回值就是从当前点到最底层的最小总和, 记录在**数组dp[i] [j] 里**

+ State: 坐标  dp [i] [j] 代表从 i,j 走到最底层的最短路径值, DP就是状态数组
+ Initialize: 最底层 `dp[n-1][i]` 可以直接从数字三角形中获取, `dp[n-1][i]=triangle[n-1][i]`, <font color = red>从最底层到最底层就是当前值</font> 
+ Function: 使用双重for 循环, 从i=n-2 到 i=0(==除了最底层以外==),  每一个j从0到 i, 此时**`dp[i][j] = Math.min(dp[i+1][j+1],dp[i+1][j]) + triangle[i][j]`** 
+ Anwer: 返回的是dp[0] [0], 自然就是最小路径总和

```java
public int minimumTotal(int[][] triangle) {

    if (triangle == null || triangle.length == 0 || triangle[0] == null || triangle[0].length == 0) {
        return 0;
    }
    // State
    int n = triangle.length;
    int [][] dp = new int[n][n];
    // Initialize
    for ( int i = 0; i < n; i++) {
        dp[n - 1][i] = triangle[n - 1][i];
    }
    // Function
    for (int i = n - 2; i >= 0; i--) {
        for (int j = 0; j < i + 1; j++) { // 第i行有i+1个元素,这里j的范围是要从0到i了
            dp[i][j] = Math.min(dp[i + 1][j], dp[i + 1][j + 1]) + triangle[i][j];
        } 
    }    
    // Answer 
    return dp[0][0];
}
```

### 解法五 多重循环(自顶向下)

需要return的value是最小路径总和的值

可以从最顶层向下走,  每个节点记录的是从起点到当前点的最短路径总和, 记录在**数组dp[i] [j] 里**

+ State: 坐标  dp [i] [j] 代表从起点走到 i,j 的最短路径值, DP就是状态数组
+ Initialize:
  + dp[0] [0] 必然是triangle[0] [0], 从起点到起点就是当前值
  +  每一层第一个 `dp[i][0]` 必然是`dp[i-1][0]+triangle[i][0]`, <font color = red>每一层第一个必然是上一层第一个+当前值</font> 
  + 每一个最后一个 `dp[i][i]`  必然是`dp[i-1][i-1]+triangle[i][i]`, <font color = red>每一层最后一个必然是上一层最后一个+当前值</font> 
+ Function: 使用双重for 循环, 从i=2 到 i=n-1 (==除了第一层以外==),每一个j从1到 i-1, 此时**`dp[i][j] = Math.min(dp[i-1][j],dp[i-1][j-1]) + triangle[i][j]`** 
+ Answer: 返回的是dp[n-1]这一行里的最小值, 因为这里每个终点

```java
public int minimumTotal(int[][] triangle) {
    if (triangle == null || triangle.length == 0 || triangle[0] == null || triangle[0].length == 0) {
        return 0;
    }
    // State
    int n = triangle.length;
    int [][] dp = new int[n][n];
    // Initialize
    dp[0][0] = triangle[0][0];
    for (int i = 1; i < n; i++) {
        dp[i][0] = dp[i - 1][0] + triangle[i][0];
        dp[i][i] = dp[i - 1][i - 1] + triangle[i][i];
    }
    // Function
    for (int i = 1; i < n; i++) {
        for (int j = 1; j < i; j++) { // 第i行有i+1个元素,所以j的范围是从1到i-1
            dp[i][j] = Math.min(dp[i - 1][j], dp[i - 1][j - 1]) + triangle[i][j];
        } 
    }    
    // Answer 
    int best = dp[n - 1][0];
    for (int i = 0; i < n; i++) {
        best = Math.min(dp[n - 1][i], best);
    }
    return best;
}
```







# 题解

时间复杂度O(n<sup>2</sup>), 空间复杂度O(n<sup>2</sup>), 来自于所有node都被存了一遍

```java
public int minimumTotal(int[][] triangle) {
    if (triangle == null || triangle.length == 0 || triangle[0] == null || triangle[0].length == 0) {
        return 0;
    }
    Map <String, Integer> map = new HashMap<>();
    return divideConquer(triangle, 0, 0, map);
}

private int divideConquer(int[][] triangle, int x, int y,Map<String, Integer> map) {
    if (x == triangle.length) {
        return 0;
    }
    String key = Integer.toString(x) + "," + Integer.toString(y);
    if (map.containsKey(key)) {
        return map.get(key);
    }
    int left = divideConquer(triangle, x + 1, y, map);
    int right = divideConquer(triangle, x + 1, y + 1, map);
    map.put(key, Math.min(left, right) + triangle[x][y]);
    return map.get(key);
}
```















