# 题目地址

https://www.lintcode.com/problem/interleaving-positive-and-negative-numbers



# 题目描述

Given an array with positive and negative integers. Re-range it to interleaving with positive and negative integers.

### Example

***Example 1***

```
Input : [-1, -2, -3, 4, 5, 6]
Outout : [-1, 5, -2, 4, -3, 6]
Explanation :  any other reasonable answer.
```

### Challenge

Do it in-place and without extra memory.

### Notice

You are not necessary to keep the original order of positive integers or negative integers.



# 思路

需要让正负数交替排列, <font color = blue>但是必须In-place完成</font>

先用一个循环查询Positive和Negative的个数,依旧使用Partition Array的思想

+ 如果positive相等或多,则把positive放在前半段, 如[1,2,3,-1,-2], 并设positiveStart = 0,negativeStart = 1
+ 如果negative多,则把negative放在前半段,如[-1,-2,-3,1,2],并设positiveStart = 1,negativeStart = 0

**怎么分?** 选用一个特定值c, <font color = red>保证c * nums[left] < 0, left++ 来减少code reuse</font>

分完之后如<font color = blue>[1,2,3,4,5,-1,-2,-3,-4] 或者 [1,2,3,4,-1,-2,-3,-4]</font>

<font color = red>注意: 此处不可能出现QuickSort一样的 nums[start] = nums[end] = pivot, 所以end必然就在start后面一位</font> 

<font color = blue>因此可以通过</font> 

再使用一次partition的思想,  对于positiveStart 和 negativeStart, 分别按+2的间隔进行检测和swap





# 题解

时间复杂度O(n),空间复杂度O(1)

```java
public void rerange(int[] A) {
    // null case
    if (A == null || A.length == 0) {
        return;
    }
	// 计算有多少个正数
    int positiveCount = 0;
    // 默认负数大于等于一半, flag = 1
    // 此时负数在第一位,negativeStart = 0
    int flag = 1, positiveStart = 1, negativeStart = 0;
	// 正数个数
    for (int i = 0; i < A.length; i++) {
        if (A[i] > 0) {
            positiveCount++;
        }
    }
	// 正数 > 一半时, 以正数为开头
    if (positiveCount > A.length / 2) {
        flag = -1;
        positiveStart = 0;
        negativeStart = 1;
    }
	
    int left = 0, right = A.length - 1;
    
    while (left <= right) {
        // 如果正数开头, flag = -1, 负正得负,left继续前进
        // 如果负数开头, flag = 1, 负正得负,left继续前进
        while (left <= right && flag * A[left] < 0) {
            left++;
        }
        // 如果正数开头, flag = -1, 负负得正,right继续左移
        // 如果负数开头, flag = 1, 正正得正,right继续左移
        while (left <= right && flag * A[right] > 0) {
            right--;
        }
        // 交换保证左右区分
        if (left <= right) {
            swap(A, left, right);
            left++;
            right--;
        }
    }
	// 不能超出长度
    while (positiveStart < A.length && negativeStart < A.length) {
        // 如果positiveStart上不是正数,就停下
        while (positiveStart < A.length && A[positiveStart] > 0) {
            positiveStart += 2;
        }
        // 如果negativeStart上不是负数,就停下
        while (negativeStart < A.length && A[negativeStart] < 0) {
            negativeStart += 2;
        }
        // 交换一下,并且移动到下一个值
        if (positiveStart < A.length && negativeStart < A.length) {
            swap(A, positiveStart, negativeStart);
            positiveStart += 2;
            negativeStart += 2;
        }

    }
}

private void swap(int[] arr, int left, int right) {
    int temp = arr[left];
    arr[left] = arr[right];
    arr[right] = temp;
}
```



### 解法二

稍稍优化,直接通过第一次partition之后的end和start来比较positive number和negative number的多少

```java
public void rerange(int[] A) {
    // write your code here
    if (A == null || A.length == 0) {
        return;
    }

    int start = 0, end = A.length - 1;
    while (start <= end) {

        while (start <= end && A[start] < 0) {
            start++;
        }
        while (start <= end && A[end] > 0) {
            end--;
        }
        if (start <= end) {
            int temp = A[start];
            A[start] = A[end];
            A[end] = temp;
        }
    }


    int positiveStart, negativeStart;
    int n = A.length - 1;

    // 负数个数是end+1, 正数个数是n-start+1
    if (n - start > end) {
        positiveStart = 0;
        negativeStart = 1;
    } else {
        negativeStart = 0;
        positiveStart = 1;
    }

    // 开始交换
    while (positiveStart <= n && negativeStart <= n) {
        while (positiveStart <= n && A[positiveStart] > 0) {
            positiveStart += 2;
        }
        while (negativeStart <= n && A[negativeStart] < 0) {
            negativeStart += 2;
        }
        // 交换
        if (positiveStart <= n && negativeStart <= n) {
            int temp = A[positiveStart];
            A[positiveStart] = A[negativeStart];
            A[negativeStart] = temp;
            positiveStart += 2;
            negativeStart += 2;
        }
    }

}
```

