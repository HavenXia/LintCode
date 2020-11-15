# 题目地址

https://www.lintcode.com/problem/ugly-number-ii/



# 题目描述

Ugly number is a number that only have prime factors `2`, `3` and `5`.

Design an algorithm to find the *n*th ugly number. The first 10 ugly numbers are `1, 2, 3, 4, 5, 6, 8, 9, 10, 12...`

### Example

**Example 1:**

```
Input: 9
Output: 10
```

**Example 2:**

```
Input: 1
Output: 1
```

### Challenge

O(*n* log *n*) or O(*n*) time.

### Notice

Note that `1` is typically treated as an ugly number.



# 思路

这里指的仅有235 factor代表着 ugly number $  = 2^{i}*3^{j}*5^{k}$

### 方法一

用一个Priority Queue 存 ugly number, n次遍历找出即可

每次遍历时都用当前的queue poll的值分别乘 2, 3, 5 并且加入回queue, 有点类似BFS

**但注意: <font color = red>可能出现重复值, 因此需要一个HashSet来校验, 而且并不是每次都按大小进去, 比如2,3,5, 之后才是4,6,10</font>**

时间复杂度O(nlogn) logn来自priority queue的poll()

**注意: 尽管return type确保了第n个是int, 但是我们一共循环了3n次, 很可能超过int, 所以要用Double 或者Long做, <font color = green>所以1需要用1D或者1L表示!!</font>**



# 题解

### Priority Queue

时间复杂度O(nlogn), 空间复杂度O(n)

```java
public int nthUglyNumber(int n) {
    // write your code here
    PriorityQueue <Double> queue = new PriorityQueue<Double>();
    queue.offer(1D);
    Set <Double> set = new HashSet <Double>();
    set.add(1D);
    int[] factor = new int[]{2,3,5};
    double cur = 0;

    for (int i = 0; i < n; i++) {
        cur = queue.poll();

        for (int num: factor) {
            double newUgly = cur * num;
            if (!set.contains(newUgly)) {
                queue.offer(newUgly);
                set.add(newUgly);
            }
        }
    }
    return (int)cur;
}
```

