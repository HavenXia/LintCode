# 题目地址

https://www.lintcode.com/problem/find-k-closest-elements



# 题目描述

Given `target`, a non-negative integer `k` and an integer array `A` sorted in ascending order, find the `k` closest numbers to `target` in `A`, sorted in ascending order by the difference between the number and target. Otherwise, sorted in ascending order by number if the difference is same.

### Example

**Example 1:**

```
Input: A = [1, 2, 3], target = 2, k = 3
Output: [2, 1, 3]
```

**Example 2:**

```
Input: A = [1, 4, 6, 8], target = 3, k = 3
Output: [4, 1, 6]
```

### Challenge

O(logn + k) time

### Notice

1. The value `k` is a non-negative integer and will always be smaller than the length of the sorted array.
2. Length of the given array is positive and will not exceed 10^4104
3. Absolute value of elements in the array will not exceed 10^4104





# 思路

先用普通二分法(Last Position 或者 First Position)找到最接近target的位置

这里出循环必然是start和end, <font color = red>注意! start和end并不一定是最近的两个值!!!</font> 

<font color = red>只能说start是小于等于target里最近的, end是大于等于target里最近的</font> 

在二分法时, 对于nums[mid] = target 时, 无论是让start还是end=mid都不影响, 因为出来的结果

<font color = blue>start一定是小于等于target的, end一定是大于等于target的</font>

**如何选择left和right?**

+ 如果start=target, 那么让left= start, <font color = blue>此时right一定不越界</font>
+ 如果end = target,那么让right=end, <font color = blue>此时left一定不越界</font>
+ <font color = red>除此之外, 无论什么情况,都一定是可以让left= start, right = end的, 因为后面都是按距离放入数组的</font>

```java
if (A[start] == target) {
    left = start;
    right = left + 1;
} else if (A[end] == target) {
    right = end;
    left = right - 1;
} else {
    left = start;
    right = end;
}
```






接着从target开始向左右遍历k次, 比较每一次的<font color = blue>绝对值差值</font>


把更小的放入result数组中,并且让指针移动一位

注意: 如果左右和target差值相同, 就要比较左右的大小,但是由于数组本身有序,所以这种时候选择左边就可以





# 题解

时间复杂度O(logn + k), 空间复杂度O(1)

```java
public int[] kClosestNumbers(int[] A, int target, int k) {
    // null case
    int[] result = new int[k];

    if (A == null || A.length == 0) {
        return result;
    }
    // 找出>= target的第一个值
    int start = 0, end = A.length - 1;
    // left和right是之后插入result用的指针
    int left, right;
    // 标准二分
    while (start + 1 < end) {
        int mid = start + (end - start) / 2;
        // 相等的时候end = mid, 确保找到的是第一个值
        if (A[mid] >= target) {
            end = mid;
        } else {
            start = mid;
        }
    }
    // 先看start再看end
    if (A[start] >= target) {
        right = start;
    } else if (A[end] >= target) {
        right = end;
    } else {
        // 如果所有的都比target小
        // 就让right == A.length
        // 这样左边就正好等于A.length - 1, 取值入int的时候就一直往左取
        // 如果是找小于等于target的最后一个值,就要用left = -1 来做
        right = A.length;
    }
    
    left = right - 1;
    
    for (int i = 0; i < k; i++) {
    	// 循环放入值
        if (isRightCloser(A, target, left, right)) {
            result[i] = A[right];
            right++;
        } else {
            result[i] = A[left];
            left--;
        }
    }
    return result;
}


private boolean isRightCloser(int[] nums, int target, int left, int right) {
    // 因为k肯定比长度小,所以不会left < 0 且 right > nums.length
    // 如果left < 0了, 那剩下的就全部从右边拿
    if (left < 0) {
        return true;
    }
    // 同理right拿完了就从左边拿
    if (right >= nums.length) {
        return false;
    }
    // 如果右边差值更小,那就从右边拿
    if (target - nums[left] > nums[right] - target) {
        return true;
    // 如果左边差值小,从左边拿
    // 或者左右相等, 左边数值更小,自然拿左边
    } else {
        return false;
    }
}
```


```

```