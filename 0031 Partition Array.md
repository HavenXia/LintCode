# 题目地址

https://www.lintcode.com/problem/partition-array



# 题目描述

Given an array `nums` of integers and an int `k`, partition the array (i.e move the elements in "nums") such that:

- All elements < *k* are moved to the *left*
- All elements >= *k* are moved to the *right*

Return the partitioning index, i.e the first index *i* nums[*i*] >= *k*.

### Example

Example 1:

```
Input:
[],9
Output:
0
```

Example 2:

```
Input:
[3,2,2,1],2
Output:1
Explanation:
the real array is[1,2,2,3].So return 1
```

### Challenge

Can you partition the array in-place and in O(n)?



# 思路

首先只需要partition一次

保证左半部分<k以及右半部分>= k即可, 严格分布

<font color = red>而Quick Sort要保证完全的排序,需要多次递归, 而且quick sort注重均匀分布</font>

**注意一定要写成left <= right,这是避免交集的重要措施**

无论奇数还是偶数的情况, 最后left都正好在第一个 `nums[i] >= k` 的index位置

当然right + 1也可以



# 题解

时间复杂度O(n),空间复杂度O(1)

```java
public int partitionArray(int[] nums, int k) {
    // write your code here
    if (nums == null || nums.length == 0) {
        return 0;
    }

    int left = 0, right = nums.length - 1;
    while (left <= right) {
        while (left <= right && nums[left] < k) {
            left++;
        }
        while (left <= right && nums[right] >= k) {
            right--;
        }
        if (left <= right) {
            int temp = nums[left];
            nums[left] = nums[right];
            nums[right] = temp;
            left++;
            right--;
        }
    }
    // left 也能写成right + 1
    return left;
}
```

