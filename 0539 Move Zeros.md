# 题目地址

https://www.lintcode.com/problem/move-zeroes



# 题目描述

Given an array `nums`, write a function to move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.

### Example

Example 1:

```
Input: nums = [0, 1, 0, 3, 12],
Output: [1, 3, 12, 0, 0].
```

Example 2:

```
Input: nums = [0, 0, 0, 3, 1],
Output: [3, 1, 0, 0, 0].
```

### Notice

1. You must do this **in-place** without making a copy of the array.
2. Minimize the total number of operations.



# 思路

需要优化修改的操作

### 如果不需要维持原数组顺序

如[0,1,0,2,3], 用partition的方法

先改成[3,1,0,2,0], 在改成[3,1,2,0,0]

一共交换了两次,有四次写入的操作

### 如果需要维持原数组顺序

如[0,1,0,2,3], <font color = blue>目标是[1,2,3,0,0]</font>

需要使用**同向双指针**的方法

<font color = red>不能使用交换的方法! 从[0, 1, 0, 2, 3] 到[1,0,0,2,3],到[1,2,0,0,3]到[1,2,3,0,0]</font>

**一共三次交换,六次写入,次数太多!**

<font color = blue>因此先把所有非0的移动过来,再把剩余的变成0, 最多也只会执行nums.length次</font>



# 题解

### 不需要维持原数组顺序

时间复杂度O(n),空间复杂度O(1)

```java
public void moveZeroes(int[] nums) {
    // null case
    if (nums == null || nums.length == 0) {
        return;
    }
    
    int left = 0, right = numbers.length - 1;
    while (left <= right) {
        while (left <= right && nums[left] != 0) {
            left++;
        }
        while (left <= right && nums[right] == 0) {
            right--;
        }
        if (left <= right) {
            int temp = nums[left];
            nums[left] = nums[right];
            nums[right] = temp;
}
```



### 需要维持原数组顺序

时间复杂度O(n), 空间复杂度O(1)

```java
public void moveZeroes(int[] nums) {
    // null case
    if (nums == null || nums.length == 0) {
        return;
    }
    int left = 0, right = 0;
    // 每次遇到不等于0的就放在左边
    while (right < nums.length) {
        if (nums[right] != 0) {
            nums[left] = nums[right];
            left++;
        }
        // 无论怎样right都要移动
        right++;
    }
	// 把剩下的全都变成0
    while (left < nums.length) {
        nums[left] = 0;
        left++;
    }
}
```

