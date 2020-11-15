# 题目地址

https://www.lintcode.com/problem/search-in-rotated-sorted-array



# 题目描述

Suppose a sorted array is rotated at some pivot unknown to you beforehand.

(i.e., `0 1 2 4 5 6 7` might become `4 5 6 7 0 1 2`).

You are given a target value to search. If found in the array return its index, otherwise return -1.

You may assume **no duplicate exists in the array.**

### Example

Example 1:

```
Input: [4, 5, 1, 2, 3] and target=1, 
Output: 2.
```

Example 2:

```
Input: [4, 5, 1, 2, 3] and target=0, 
Output: -1.
```

### Challenge

O(logN) time





# 思路

### 方法一

可以i直接沿用[Find Minumum in Rotated Sorted Array](/Users/parallax/Documents/CS/LintCode/0159 Find Minimum in Rotated Sorted Array.md)的思路

先写一个函数,用来找出最小值的index

+ 如果<font color = red>数组最后一个数大于target, 那就对index到最后进行标准二分</font>

+ 如果<font color = red>数组最后一个数小于target, 那就对开头到index - 1进行标准二分</font>

### 方法二

直接在一次二分钟全部完成, 依旧start = 0, end = nums.length - 1

<font color = red>无论target在左右区间,都能一共分成三个区间来看待</font>

条件依旧是start + 1 < end

+ 如果target > nums[end], 那么target在左区间

  + 如果nums[mid] > target (<font color = blue>第二区间</font>)或者nums[mid] < nums[end] (<font color = blue>第三区间</font>), <font color = red>end = mid</font> 

  + 如果 nums[end] <=(<font color = green>这里其实不可能=</font>) nums[mid] <= target(<font color = blue>第二区间</font>),<font color = red>start = mid</font>

+ 如果target <= nums[end], 那么target在右区间
  + 如果nums[mid] < target (<font color = blue>第二区间</font>)或者nums[mid] > nums[end] (<font color = blue>第一区间</font>), <font color = red>start = mid</font> 
  + 如果 target <= nums[mid] <=(<font color = green>这里其实不可能=</font>) nums[end] (<font color = blue>第三区间</font>), <font color = red>end = mid</font>

出循环之后, 分别对start和end进行检测即可



# 题解

### 方法一

时间复杂度O(logn),空间复杂度O(1)

```java
public int search(int[] A, int target) {
    // write your code here
    if (A == null || A.length == 0) {
        return -1;
    }

    int index = findMinimumIndex(A);
	//  9-11行其实可以去掉, target == 最后一个数也能归类到右区间进行二分
    if (target == A[A.length - 1]) {
        return A.length - 1;
    }
    if (target <= A[A.length - 1]) {
        return binarySearch(A, index, A.length - 1, target);
    } else {
        return binarySearch(A, 0, index - 1, target);
    }
}
// 见0159 minimum in rotated sorted array
private int findMinimumIndex(int[] nums) {

    int start = 0, end =  nums.length - 1;
    if (nums[start] < nums[end]) {
        return start;
    }

    while (start + 1 < end) {
        int mid = start + (end - start) / 2;
        if (nums[mid] < nums[end]) {
            end = mid;
        } 
        if (nums[mid] > nums[end]) {
            start = mid;
        }
    }

    if (nums[start] < nums[end]) {
        return start;
    } else {
        return end;
    }
}

// 标准二分
private int binarySearch(int[] nums, int start, int end, int target) {

    while (start + 1 < end) {

        int mid = start + (end - start) / 2;

        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] > target) {
            end = mid;
        } else {
            start = mid;
        }
    }

    if (nums[start] == target) {
        return start;
    }
    if (nums[end] == target) {
        return end;
    }
    return -1;
}
```



### 方法二

时间复杂度O(logn),空间复杂度O(1)

```java
public int search(int[] A, int target) {
    // null case
    if (A == null || A.length == 0) {
        return -1;
    }
	
    int start = 0, end = A.length - 1;

    while (start + 1 < end) {
        int mid = start + (end - start) / 2;

        if (target > A[end]) {
            if (A[mid] > target || A[mid] < A[end]) {
                end = mid;
            } else {
                start = mid;
            }
            
        } else {
            if (A[mid] <= target || A[mid] > A[end]) {
                start = mid;
            } else {
                end = mid;
            }
        }
    }

    if (A[start] == target) {
        return start;
    }
    if (A[end] == target) {
        return end;
    }

    return -1;
}
```

