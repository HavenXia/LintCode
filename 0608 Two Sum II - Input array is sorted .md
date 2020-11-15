# 题目地址

https://www.lintcode.com/problem/two-sum-ii-input-array-is-sorted



# 题目描述

Given an array of integers that is already *sorted in ascending order*, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.

### Example

**Example 1:**

```
Input: nums = [2, 7, 11, 15], target = 9 
Output: [1, 2]
```

**Example 2:**

```
Input: nums = [2,3], target = 5
Output: [1, 2]
```

### Notice

You may assume that each input would have exactly one solution.



# 思路

### 普通思路

O(n)复杂度, 按照双指针start和end从两侧向中间遍历

+ 如果 `nums[start] + nums[end]  == target` ,就直接返回
+ 如果 `nums[start] + nums[end]  < target` , start++
+ 反之target--

**注意:这里返回的不是index, 而是第几位, 其实就是index + 1**







# 题解

### 普通思路

时间复杂度O(n),空间复杂度O(1)

```java
public int[] twoSum(int[] nums, int target) {
    // null case
    if (nums == null || nums.length == 0) {
        return null;
    }

    int start = 0, end = nums.length - 1;
    while (start < end) {
        if (nums[start] + nums[end]  == target) {
            return new int[]{start + 1, end + 1};
        }

        if (nums[start] + nums[end]  < target) {
            start++;
        }

        if (nums[start] + nums[end]  > target) {
            end--;
        }
    }
    return null;
}
```









