# 题目地址

https://www.lintcode.com/problem/sort-colors-ii



# 题目描述

Given an array of *n* objects with *k* different colors (numbered from 1 to k), sort them so that objects of the same color are adjacent, with the colors in the order 1, 2, ... k.

### Example

**Example1**

```
Input: 
[3,2,2,1,4] 
4
Output: 
[1,2,2,3,4]
```

**Example2**

```
Input: 
[2,1,1,2,2] 
2
Output: 
[1,1,2,2,2]
```

### Challenge

A rather straight forward solution is a two-pass algorithm using counting sort. That will cost O(k) extra memory. Can you do it without using extra memory?

### Notice

1. You are not suppose to use the library's sort function for this problem.
2. `k` <= `n`



# 思路

先猜时间复杂度

+ k = 1, 一种颜色, O(1)
+ k = 2, 两种颜色, O(n)
+ k = 3, 三种颜色(见Sort Colors), O(n)
+ k = n, 每个颜色都不一样,就相当于快排, O(nlogn)

<font color = red>可猜时间复杂度为O(nlogk)</font>

### 分治法

**和快速排序的区别在哪?**

这里是按照颜色的种类(<font color = blue>或者可以说是数组中不同数的种类</font>), 然后进行一次又一次的partition

如果每个数都不一样,那实际上就是快速排序本身

<font color = red>正因为这里每个数有可能一样,所以时间复杂度O(nlogk)会低于快排一点</font>

因为是对于k来作为pivot, <font color = blue>且k种颜色的number就是1到k</font>, <font color = red>如果不是连续的1-k就不能这么做!</font>

所以第一次的pivot就是(1 + k) / 2



# 题解

时间复杂度O(nlogk),空间复杂度O(1)

```java
public void sortColors2(int[] colors, int k) {
    // write your code here
    if (colors == null || colors.length == 0) {
        return;
    }
    rainbowSort(colors, 0, colors.length - 1, 1, k);
}

private void rainbowSort(int[] colors, int left, int right, int colorStart, int colorEnd) {
	// 设置两个出口
    if (colorStart >= colorEnd) {
        return;
    }
	// 其实当colorStart 等于 colorEnd时, left必然等于right
    // 但是写一下是不会有问题的
    if (left >= right) {
        return;
    }
	// 这里要用colorMid来区分,而不是普通的mid
    int l = left, r = right;
    int colorMid = colorStart + (colorEnd - colorStart) / 2;

    while (l <= r) {
        while (l <= r && colors[l] <= colorMid) {
            l++;
        }
        while (l <= r && colors[r] > colorMid) {
            r--;
        }
        if (l <= r) {
            int temp =colors[l];
            colors[l] = colors[r];
            colors[r] = temp;
            l++;
            r--;
        }
    }
	// 继续递归
    rainbowSort(colors, left, r, colorStart, colorMid);
    rainbowSort(colors, l, right, colorMid + 1, colorEnd);
}
```

