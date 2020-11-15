# 题目地址

https://www.lintcode.com/problem/search-in-a-big-sorted-array



# 题目描述

Given a big sorted array with non-negative integers sorted by non-decreasing order. The array is so big so that you can not get the length of the whole array directly, and you can only access the kth number by `ArrayReader.get(k)` (or ArrayReader->get(k) for C++).

Find the first index of a target number. Your algorithm should be in O(log k), where k is the first index of the target number.

Return -1, if the number doesn't exist in the array.

### Example

**Example 1:**

```
Input: [1, 3, 6, 9, 21, ...], target = 3
Output: 1
```

**Example 2:**

```
Input: [1, 3, 6, 9, 21, ...], target = 4
Output: -1
```

### Challenge

O(logn) time, n is the first index of the given target number.

### Notice

If you accessed an inaccessible index (outside of the array), ArrayReader.get will return `2,147,483,647`.



# 思路

使用倍增法确定右边界

首先设置参数<font color = blue>kth = 1</font>, 然后不断get( kth - 1), 如果小于target则kth *= 2(<font color = red>倍增找右端点</font>)

此处用小于意味着只要遇到大于等于target就停下来,因为只需要first index of target

然后按照标准二分法

以start = 0(<font color = blue>或者 kth / 2, 因为kth / 2就是倒数第二次判定, 必然小于target的</font>), end = kth - 1

接着进行二分法

只要大于length的都返回了Integer.MAX_VALUE, 所以不影响

时间复杂度为O(logk), 空间复杂度O(1)





# 题解

```java
public int searchBigSortedArray(ArrayReader reader, int target) {
    // 记得用while来找出index
    // 此处不用null case, 如果空的, start = 0, end = 1, 也是返回 -1
    int index = 1;
    while (reader.get(index - 1) < target) {
        index *= 2;
    } 
	// end是右边界
    int start = 0, end = index - 1;
	// 标准二分
    while (start + 1 < end) {
        int mid = start + (end - start) / 2;

        if (reader.get(mid) == target) {
            end = mid;
        } else if (reader.get(mid) < target) {
            start = mid;
        } else {
            end = mid;
        }
    }
    // 先看start, 因为查找最初index
    if (reader.get(start) == target) {
        return start;
    } 
    if (reader.get(end) == target) {
        return end;
    }
    return -1;
}
```

