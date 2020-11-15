# 题目地址

https://www.lintcode.com/problem/last-position-of-target



# 题目描述

Find the last position of a `target` number in a sorted array. Return `-1` if target does not exist.

### Example

**Example 1:**

```
Input: nums = [1,2,2,4,5,5], target = 2
Output: 2
```

**Example 2:**

```
Input: nums = [1,2,2,4,5,5], target = 6
Output: -1
```



# 思路

### 非递归二分法

模板见[二分法](/Users/parallax/Documents/CS/九章算法课/03 二分法,递推.md)



# 题解

### 非递归二分法

时间复杂度O(logn), 空间复杂度O(logn)

```java
public int findPosition(int[] nums, int target) {
    // write your code here
    if (nums == null || nums.length == 0) {
        return -1;
    }
    int start = 0, end = nums.length - 1;
    while (start + 1 < end) {
        int mid = start + (end - start) / 2;
        if (nums[mid] == target) {
            start = mid; // 修改1: start = mid;
        } else if (nums[mid] > target) {
            end = mid;
        } else {
            start = mid;
        }
    }
    if (nums[end] == target) {  // 修改2: 出循环后先检测end在检测target
        return end; 
    }
    if (nums[start] == target) {
        return start;
    }
    return -1;
}
```

