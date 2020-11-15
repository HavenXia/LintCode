# 题目地址

https://www.lintcode.com/problem/triangle-count



# 题目描述

Given an array of integers, how many three numbers can be found in the array, so that we can build an triangle whose three edges length is the three numbers that we find?

### Example

**Example 1:**

```
Input: [3, 4, 6, 7]
Output: 3
Explanation:
They are (3, 4, 6), 
         (3, 6, 7),
         (4, 6, 7)
```

**Example 2:**

```
Input: [4, 4, 4, 4]
Output: 4
Explanation:
Any three numbers can form a triangle. 
So the answer is C(3, 4) = 4
```



# 思路

同样是abc, 不过要求是a+b > c(<font color = blue>两小边之和大于第三边</font> )

对c进行遍历,而且此次对于c**不需要去重**,同时对ab也不需要去重

在findTwoSum(int[] arr, int index)中, left是0, right是index - 1,

对于[1,2,3,4,5,6]  比如c = 7, 一旦 arr[left] + arr[right] > c , 对于当前的right所有的left都可以实现

<font color = red>count可以直接+ right - left</font>, **然后right--**

如果arr[left] + arr[right] < c则对于当前left剩下所有的right都不满足,**left只能前进**

<font color = red>注意:本题只能对C进行遍历然后ab进行twoSum, 因为c > a + b, 所以如果没达到的话, 只能a move forth</font> 

如果想着以A进行遍历, 然后bc 进行twoSum, 如果没达到 a + b > c的话, b和c有两个方向可以走, 无法抉择



# 题解

时间复杂度O(n<sup>2</sup>), 空间复杂度O(1)

```java
public int triangleCount(int[] S) {
    // null case
    if (S == null || S.length == 0) {
        return 0;
    }
    Arrays.sort(S);
    // left和right直接在外层声明
    int left, right;
    int count = 0;
    for (int i = S.length -1; i > 1; i--) {
        // 每次都变动left和right
        left = 0;
        right = i - 1;
        // 常规查找
        while (left < right) {
            if (S[left] + S[right] > S[i]) {
                count += right - left;
                right--;
            } else {
                left++;
            }
        }
    }
    return count;
}
```

