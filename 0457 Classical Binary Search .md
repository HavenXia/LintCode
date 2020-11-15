# 题目地址

https://www.lintcode.com/problem/classical-binary-search



# 题目描述

Find any position of a target number in a sorted array. Return `-1` if target does not exist.

### Example

**Example 1:**

```
Input: nums = [1,2,2,4,5,5], target = 2
Output: 1 or 2
```

**Example 2:**

```
Input: nums = [1,2,2,4,5,5], target = 6
Output: -1
```

### Challenge

O(logn) time



# 思路

经典二分法

### 递归二分法

+ 定义递归函数
+ 递归拆分
+ 递归出口设置

<font color = blue>此处出口 start == end实质上就是递归过程中的mid == start,但是这样写更明了</font>

### 非递归二分法

使用标准模板, 见[二分法](/Users/parallax/Documents/CS/九章算法课/03 二分法,递推.md)



# 题解

### 递归二分法

时间复杂度O(logn), 空间复杂度O(logn)

```java
public int findPosition(int[] nums, int target) {
    // write your code here

    if (nums == null || nums.length == 0) { // 设置null case
        return -1;
    }
    // 1.递归定义
    return binarySearch(nums, 0, nums.length - 1, target);
}


private int binarySearch(int[] nums, int start, int end, int target) {
	// 3. 递归出口
    if (start > end) { // 如果大于了一定是没找到
        return -1;
    }

    if (start == end) { // 如果等于的话就看一下是否正好相等,是的话则返回
        if (nums[start] == target) {
            return start; // 其实这步和mid == start 一样,只是确保逻辑
        }
        return -1;
    }
	// 2.递归拆分
    int mid = start + (end - start) / 2;
    // 如果直接找到就直接返回
    if (nums[mid] == target) {
        return mid;
    }
    if (nums[mid] > target) { // 搜索左区间
        return binarySearch(nums, start, mid, target);
    }
	// 否则就对右区间搜索
    return binarySearch(nums, mid + 1, end, target);
}
```



### 非递归二分法

时间复杂度O(logn), 空间复杂度O(1)(<font color = red>尾递归, 递归深入后上一层销毁</font>)

代码完全等同于

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

