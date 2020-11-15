# 题目地址

https://www.lintcode.com/problem/find-peak-element



# 题目描述

There is an integer array which has the following features:

- The numbers in adjacent positions are different.
- A[0] < A[1] && A[A.length - 2] > A[A.length - 1].

We define a position P is a peak if:

```
A[P] > A[P-1] && A[P] > A[P+1]
```

Find a peak element in this array. Return the index of the peak.

### Example

```
Example 1:
	Input:  [1, 2, 1, 3, 4, 5, 7, 6]
	Output:  1 or 6
	
	Explanation:
	return the index of peek.


Example 2:
	Input: [1,2,3,4,1]
	Output:  3
```

### Challenge

Time complexity O(logN)

### Notice

- It's guaranteed the array has at least one peak.
- The array may contain multiple peeks, find any of them.
- The array has at least 3 numbers in it.



# 思路

本体意思是找出<font color = blue>任意一个峰</font>

+ 如果要求找出最大值, 难以O(logn), 因为这就相当于在不规则数组中找出最大值
+ 如果要求找出所有的峰, 也难以O(logn),比如有N/2个峰,就至少需要<font color = blue>O(N/2)</font>

由题目可知, 数组开头是上升趋势,末尾是下降趋势

依旧start和end, 如果nums[mid]直接满足峰值关系,就返回mid

+ 如果num[mid] < nums[mid - 1],下降趋势, **左区间必定有峰**,这个一定要想通!!!!
+ 如果nums[mid] < nums[mid + 1]上升趋势, 右区间必定有峰

<font color = red>注意此时的else就是nums[mid - 1] < nums[mid] && nums[mid + 1] < nums[mid], 即峰值条件</font>

**什么时候出循环?**

<font color = blue>**此题中峰值不可能在start或end, 因此只要存在就一定会被找出!!所以出循环一定是找不到**</font>

<font color = blue>**因此不需要比较start和end, 直接return -1 即可**</font>



# 题解

时间复杂度O(logn), 空间复杂度O(1)

```java
public int findPeak(int[] A) {
    // 不需要null case
    int start = 0, end = A.length - 1;

    while (start + 1 < end) {
        int mid = start + (end - start) / 2;
		// mid右侧上升则去右区间
        if (A[mid] < A[mid + 1]) {
            start = mid;
        // mid左侧下降则去左区间
        } else if (A[mid] < A[mid - 1]) {
            end = mid;
        // 直接返回mid
        } else {
            return mid;
        }
    }
	// 出循环只能是找不到的情况
    return -1;
}
```

