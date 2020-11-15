# 题目地址

https://www.lintcode.com/problem/maximum-number-in-mountain-sequence



# 题目描述

Given a mountain sequence of `n` integers which increase firstly and then decrease, find the mountain top.

### Example

Example 1:

```
Input: nums = [1, 2, 4, 8, 6, 3] 
Output: 8
```

Example 2:

```
Input: nums = [10, 9, 8, 7], 
Output: 10
```

### Notice

Arrays are strictly incremented, strictly decreasing



# 思路

普通遍历都需要O(n)的时间复杂度

因此依旧使用不递归二分法的思想

必须保证O(logn)的时间复杂度

循环条件依旧用 `start + 1 < end` , <font color = red>这能保证start到end至少有三个元素</font> 

mid = start + (end - start) / 2 <font color = red>**必定不是start,也不是end**</font>

由题目可以知道每个element都不一样

所以mid - 1和mid + 1都是存在的,比较mid和其一判断即可(<font color = blue>nums[mid] == nums[mid - 1] 不存在</font>)

这里出循环就是保证是山峰和左右某一个值, 接着比较即可

# 题解

### 非递归二分法

时间复杂度O(logn), 空间复杂度O(1)

```java
public int mountainSequence(int[] nums) {
    // write your code here
    if (nums == null || nums.length == 0) {
        return -1;
    }

    int start = 0, end = nums.length - 1;


    while (start + 1 < end) {

        int mid = start + (end - start) / 2;

        if (nums[mid] > nums[mid - 1]) {
            start = mid;
        } else {
            end = mid;
        }
    }
    if (nums[start] > nums[end]) {
        return nums[start];
    } 
    return nums[end];
}
```

