# 题目地址

https://www.lintcode.com/problem/two-sum-closest-to-target



# 题目描述

Given an array `nums` of *n* integers, find two integers in *nums* such that the sum is closest to a given number, *target*.

Return the absolute value of difference between the sum of the two integers and the target.

### Example

**Example1**

```
Input:  nums = [-1, 2, 1, -4] and target = 4
Output: 1
Explanation:
The minimum difference is 1. (4 - (2 + 1) = 1).
```

**Example2**

```
Input:  nums = [-1, -1, -1, -4] and target = 4
Output: 6
Explanation:
The minimum difference is 6. (4 - (- 1 - 1) = 6).
```

### Challenge

Do it in O(nlogn) time complexity.



# 思路

看似求近似变得麻烦实则不然,依旧使用了普通的双指针

先进行排序,然后设一个 `diff = Integer.MAX_VALUE` ,接着

+ 如果 `nums[left] + nums[right] == target` ,即可直接返回0

+ 如果 `nums[left] + nums[right] > target` , <font color = blue>对于当前的right,所有的left都过大了, 因此right--,</font>  

  <font color = blue>同时用 `Math.max(diff, target - sum)` 来求出新的最小绝对差值</font> 

+ 如果 `nums[left] + nums[right] < target` , <font color = blue>对于当前的left,所有的right都过小了, 因此left++,</font>  

  <font color = blue>同时用 `Math.max(diff, target - sum)` 来求出新的最小绝对差值</font> 

**每次只要不等于target都需要计算一次diff, 确保所有的pair都被尝试**





# 题解

时间复杂度O(nlogn),空间复杂度O(1)

```java
public int twoSumClosest(int[] nums, int target) {
    // null case
    if (nums == null || nums.length <= 1) {
        return -1;
    }
    Arrays.sort(nums);
    int left = 0, right = nums.length - 1;
    int diff = Integer.MAX_VALUE;
    int sum;
	
    while (left < right) {

        sum = nums[left] + nums[right];

        if (sum == target) {
            return 0;
        }
        // 只要不相等就得直接
        if (sum > target) {
            diff = Math.min(diff, Math.abs(target - sum));
            right--;
        }
        if (sum < target) {
            diff = Math.min(diff, Math.abs(target - sum));
            left++;
        }
    }

    return diff;
}
```

