# 题目地址

https://www.lintcode.com/problem/find-minimum-in-rotated-sorted-array



# 题目描述

Suppose a sorted array in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., `0 1 2 4 5 6 7` might become `4 5 6 7 0 1 2`).

Find the minimum element.

### Example

**Example 1:**

```
Input：[4, 5, 6, 7, 0, 1, 2]
Output：0
Explanation：
The minimum value in an array is 0.
```

**Example 2:**

```
Input：[2,1]
Output：1
Explanation：
The minimum value in an array is 1.
```

### Notice

You can assume no duplicate exists in the array.



# 思路

<font color = blue>题目要求找到转置之后的那第一个位置, 如[4,5,6,7,0,1,2]中的0</font>

依旧是使用二分法

先判断是否nums[start] < nums[end],是的话都不用进循环

在start + 1 < end的条件下, nums[mid + 1]是必定存在的

只要nums[mid] > nums[mid + 1], <font color = red>就找到了这个mid</font>

**但是如何选择左右区间?**

+ 如果nums[mid] > nums[right], 那选右边, start = mid
+ 如果nums[mid] < nums[right], 那选左边, end = mid;

**出循环的情况有哪些?**

+ 只有一个或者两个元素
+ [2,3,4,1]这种最小值在end的情况(<font color = red>最小值在start的已经在上面被过滤了</font>)

<font color = red>**甚至17-19行可以直接舍去, 这样无论怎么样出循环的时候start和end都有一个为最小值,再比较即可**</font>

### Follow up

如果有重复的数字还能用二分吗?

<font color = red>不行, 因为mid可能会大于等于end, 比如[1,1,1,0,1], 用二分根本查不出,每次查到1都不能确定是否在左区间还是右区间只能用O(n)</font>





# 题解

时间复杂度O(logn),空间复杂度O(1)

```java
public int findMin(int[] nums) {
    // write your code here
    if (nums == null || nums.length == 0) {
        return -1;
    }

    int start = 0, end = nums.length - 1;
	// 先判断一下是不是完全increasing
    if (nums[start] < nums[end]) {
        return nums[start];
    }

    while (start + 1 < end) {
        int mid = start + (end - start) / 2;
        
        // 17-19行可以直接删去,因为出循环必然是start和end两个值且必定包括最小值
        if (nums[mid] < nums[mid - 1] && nums[mid] > nums[mid + 1]) {
            return nums[mid];
        }
        //
        // mid 更大的话,意味着最小值在右半段
        if (nums[mid] > nums[end]) {
            start = mid;
        }
        if (nums[mid] < nums[end]) {
            end = mid;
        }
    }
	// 这里是把只有一个元素也考虑了进去, 那样的话必然start == end, 返回end也就是唯一值
    if (nums[start] < nums[end]) {
        return nums[start];
    } else {
        return nums[end];
    }
}
```

