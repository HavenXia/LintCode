# 题目地址

https://www.lintcode.com/problem/first-position-of-target



# 题目描述

For a given sorted array (ascending order) and a `target` number, find the first index of this number in `O(log n)` time complexity.

If the target number does not exist in the array, return `-1`.

### Example

```
Example 1:
	Input:  [1,4,4,5,7,7,8,9,9,10]，1
	Output: 0
	
	Explanation: 
	the first index of  1 is 0.

Example 2:
	Input: [1, 2, 3, 3, 4, 5, 10]，3
	Output: 2
	
	Explanation: 
	the first index of 3 is 2.

Example 3:
	Input: [1, 2, 3, 3, 4, 5, 10]，6
	Output: -1
	
	Explanation: 
	Not exist 6 in array.
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
            end = mid; // 修改1: end = mid;
        } else if (nums[mid] > target) {
            end = mid;
        } else {
            start = mid;
        }
    }
    if (nums[start] == target) {  // 修改2: 出循环后先检测start在检测end
        return start; 
    }
    if (nums[end] == target) {
        return end;
    }
    return -1;
}
```

