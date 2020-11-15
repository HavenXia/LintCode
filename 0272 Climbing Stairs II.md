# 题目地址

https://www.lintcode.com/problem/climbing-stairs-ii



# 题目描述

A child is running up a staircase with `n` steps, and can hop either 1 step, 2 steps, or 3 steps at a time. Implement a method to count how many possible ways the child can run up the stairs.

### Example

**Example 1:**

```plain
Input: 3
Output: 4
Explanation: 1 + 1 + 1 = 2 + 1 = 1 + 2 = 3 = 3 , there are 4 ways.
```

**Example 2:**

```plain
Input: 4
Output: 7
Explanation: 1 + 1 + 1 + 1 = 1 + 1 + 2 = 1 + 2 + 1 = 2 + 1 + 1 = 2 + 2 = 1 + 3 = 3 + 1 = 4 , there are 7 ways.
```

### Clarification

For `n=0`, we think the answer is 1.



# 思路

同样使用记忆化搜索来做

用类似[Bash Game](/Users/parallax/Documents/求职/LintCode/1300 Bash game.md)的方法来做, 每个n所返回的排列总数 = (n-1)的总数 + (n-2)的总数 + (n-3)的总数

+ 对于n-1个, 相当于加一个1 step的到达n
+ 对于n-2个, 相当于加一个2 step的到达n, ==为什么这里不能两个1step?== 因为那已经出现在(n-1) 加一个step中了
+ 对于n-3个, 相当于加一个3 step的到达n,同理其他的组合也已经出现过了

三个加起来一起存到memo map中, Map<Integer, Integer>

Edge case:

+ 每次进入先检测下 i == 0 /1/2, 分别对应1/1/2
+ 如果存在于memo里, 就直接返回memo.get



# 题解

时间复杂度O(n),递归深度O(n), 但是这里并没有风险

```java
public int climbStairs2(int n) {
    // write your code here
    return memoSearch(n, new HashMap());
}

private int memoSearch(int i, Map<Integer,Integer> memo) {
    if (i == 0 || i == 1) {
        return 1;
    }
    if (i == 2) {
        return 2;
    }
    if (memo.containsKey(i)) {
        return memo.get(i);
    }

    int num = memoSearch(i - 1,memo) + memoSearch(i - 2, memo) + memoSearch(i - 3, memo);
    memo.put(i, num);

    return num;
}
```









