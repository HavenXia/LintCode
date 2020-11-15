# 题目地址

https://www.lintcode.com/problem/two-sum-less-than-or-equal-to-target



# 题目描述

Given an array of integers, find how many pairs in the array such that their sum is `less than or equal to` a specific target number. Please return the number of pairs.

### Example

Example 1:

```
Input: nums = [2, 7, 11, 15], target = 24. 
Output: 5. 
Explanation:
2 + 7 < 24
2 + 11 < 24
2 + 15 < 24
7 + 11 < 24
7 + 15 < 24
```

Example 2:

```
Input: nums = [1], target = 1. 
Output: 0. 
```





# 思路

依然是排序接着双指针

排序之后如 `{1,3,4,5,6,8,14}`

设置left和right两个指针

如果 `arr[left] + arr[right] > target `, <font color = blue>意味着right过大,需要`right--`</font>

如果 `arr[left] + arr[right] <= target `, <font color = blue>意味着对于left所有剩下元素都可以选择,因此 `left++`</font> ,<font color = red>同时 `count += right - left`</font> 

此处的right - left即为对于nums[left] 可以选择的元素个数





# 题解

时间复杂度O(n),空间复杂度O(1)

```java
public int twoSum5(int[] nums, int target) {
    // write your code here

    if (nums.length == 1) { // 排除只有一个元素
        return 0;
    }

    Arrays.sort(nums); // 排序

    int count = 0; 
    int left = 0, right = nums.length - 1;
    while (left < right) {
        if (nums[left] + nums[right] > target) { // 左 + 右大于target则right减少一个
            right--;
        }else{
            count += right - left; // 加上所有对于left可以选择的个数
            left++; //left前进
        }
    }

    return count;
}
```

