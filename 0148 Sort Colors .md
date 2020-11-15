# 题目地址

https://www.lintcode.com/problem/sort-colors



# 题目描述

Given an array with *n* objects colored *red*, *white* or *blue*, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers `0`, `1`, and `2` to represent the color red, white, and blue respectively.

### Example

***Example 1***

```
Input : [1, 0, 1, 2]
Output : [0, 1, 1, 2]
Explanation : sort it in-place
```

### Challenge

A rather straight forward solution is a two-pass algorithm using counting sort.
First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.

Could you come up with an one-pass algorithm using only constant space?

### Notice

You are not suppose to use the library's sort function for this problem.
You should do it in-place (sort numbers in the original array).





# 思路

+ 使用快速排序or归并排序, 直接或者排序好的0,1,2, 时间复杂度O(nlogn), 空间复杂度O(logn)

+ <font color = blue>使用三指针进行排序,分别为left,right和一个移动的mid指针</font>

  排序完成后做到left左边都为0,right右边都为2(<font color = green>不包含left和right</font>)

  + 首先当 `nums[mid] = 1 `时,mid++

  + 当 `nums[mid] < 1 `时, 将nums[mid] 和 nums[left]交换, 并且mid和left都右移

  + 当  `nums[mid] > 1 `时,将nums[mid] 和 nums[right]交换,<font color = blue>right左移但mid不动</font>

    <font color = red>因为有可能是 `nums[mid] == 2, nums[right] == 0`,即使交换了也得继续和left检验一下</font>

    **前置条件为while (mid <= right),如果用mid<right会导致[1,0]无法交换**





# 题解

时间复杂度为O(n),空间复杂度O(1)

```java
public void sortColors(int[] nums) {
    // write your code here
    if (nums == null || nums.length == 0) { // null case排除
        return;
    }

    int left = 0, mid = left, right = nums.length - 1;
    while (mid <= right) { // 必须用小于等于

        while (mid <= right && nums[mid] < 1) { // 此处的while其实用if也可以,都是O(n)
            swap(nums, mid, left);
            left++;
            mid++;
        }

        while (mid <= right && nums[mid] > 1) { //
            swap(nums, mid, right);
            right--;
        }

        while (mid <= right && nums[mid] == 1) {
            mid++;
        }
    }
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

