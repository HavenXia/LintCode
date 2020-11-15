# 题目地址

https://www.lintcode.com/problem/median-of-two-sorted-arrays



# 题目描述

There are two sorted arrays *A* and *B* of size *m* and *n* respectively. Find the **median** of the two sorted arrays.

### Example

**Example 1**

```plain
Input:
A = [1,2,3,4,5,6]
B = [2,3,4,5]
Output: 3.5
```

**Example 2**

```plain
Input:
A = [1,2,3]
B = [4,5]
Output: 3
```

### Challenge

The overall run time complexity should be `O(log (m+n))`.

### Clarification

- **The definition of the median:**
- The median here is equivalent to the median in the mathematical definition.
- The median is the middle of the sorted array.
- If there are n numbers in the array and n is an odd number, the median is A[(n-1)/2].
- If there are n numbers in the array and n is even, the median is (A[n / 2] + A[n / 2 + 1]) / 2.
- For example, the median of the array A=[1,2,3] is 2, and the median of the array A=[1,19] is 10.



# 思路

### 归并排序

双指针归并实现merge排序,用一个left指针和一个right指针分别指向两个Array的开头

当 `left >= A.length || right < B.length && A[left] > B[right]` , right++

反之, else的情况下都是left++, <font color = blue>这种类型的题目只要想好一个指针的条件剩下一个用else就可以</font> 

并且需要nowNum来在right和left变化前变成对应值, <font color = blue>每次循环开始的时候要让preNum = nowNum来确保紧跟</font>

因为找的是中位数, 所以要遍历 (A.length + B.length) / 2 + 1次

如果总长度是奇数, 就return rightNum, 如果是偶数就返回(leftNum + rightNum) / 2.0

时间复杂度O(m + n), 空间复杂度O(1), <font color = red>太慢了</font> 

### 二分

假设数组AB长度分别为n1, n2, 那么总长度是n1+n2, <font color = blue>但是对于合并数组C而言,index是到n1+n2-1</font>

定义K = (n1 + n2) / 2, <font color = blue>如果C的长度是偶数, k指向的就是右中位数,k-1指向左中位数</font>

​									  <font color = blue>如果C的长度是奇数, k指向的中位数</font>

可以理解为从A中拿出m1个元素(<font color = blue>A[0]到A[m1 - 1]</font>),从B中拿出m2个元素(<font color = blue>B[0]到B[m1 - 1]</font>), m1+m2 = k, 所以这些元素构成了<font color = blue>C[0]到C[k - 1]</font>

<font color = red>不难得出, C[k - 1]就是Math.max(A[m1 - 1], B[m2 - 1]), 而C[k]就是Math.min(A[m1], B[m2]), **但并不意味着C[k-1]是在A[m1 - 1], B[m2 - 1]中二选一, 因为有可能最后两个值都来自同一数组**</font>

+ 取left = 0, right = A.length, 循环条件为m1 <= A.length
+ 如果A[m1] < B[m2 - 1], <font color = blue>那说明A数组的m1还是选小了, 让left = m1 + 1</font>

+ 如果A[m1 - 1] > B[m2], <font color = blue>那说明A数组的m1选大了, 让right = m1 - 1 </font>

+ 如果都符合, 判断总长度是偶数还是奇数
  + 偶数: 返回(max(A[m1 - 1], B[m2 - 1]) + min(A[m1], B[m2])) / 2
  + 奇数: 返回min(A[m1], B[m2]), <font color = blue>因为m1 + m2个元素拿出来放在左边, 下一个就是中位数, 即A[m1]和B[m2]中较小的那个</font>

<font color = red>**以上这套思想仅适用于n1 <= n2, 因为如果n1 > n2, 那么在m1较小的时候必然出现m2 > n2, OutOfBound的情况**</font>

因此在算法开头就要判断下是否n1 > n2, 如果是的话, 直接返回findMedianSortedArrays(int[] B, int[] A)









# 题解

### 归并排序

时间复杂度O(m + n), 空间复杂度O(1)

```java
public double findMedianSortedArrays(int[] A, int[] B) {
    // 本题可以不考虑null case
    int left = 0, right = 0;
    int preNum = 0, nowNum = 0;
    // 归并排序
    // 找到中位数一共要遍历
    for (int i = 0; i < (A.length + B.length) / 2 + 1; i++) {
        preNum = nowNum;
        // 只需想出一边的就行
        if (left >= A.length || right < B.length && A[left] > B[right]) {
            nowNum = B[right];
            right++;
        } else {
            nowNum = A[left];
            left++;
        }
    }
    if ((A.length + B.length) % 2 == 0) {
        return (preNum + nowNum) / 2.0;
    } else {
        return nowNum;
    }
}
```



### 二分

时间复杂度O(log(m+n)), 空间复杂度O(1)

```java
public double findMedianSortedArrays(int[] A, int[] B) {
    if (A.length > B.length) {
        return findMedianSortedArrays(B, A);
    }
    // 声明左右指针和两个cut
    // cut的值代表左边有多少个值
    int len = A.length + B.length;
    int left = 0, right = A.length;
    int m1 = 0, m2 = 0;

    // cut的值最大能到length
    while (m1 <= A.length) {
        // 二分法
        m1 = left + (right - left) / 2;
        m2 = len / 2 - m1;
        // 考虑m1 == 0 时候A[m1 - 1]不存在的情况
        // 用MINVALUE可以确保L1 
        double L1 = (m1 == 0) ? Integer.MIN_VALUE : A[m1 - 1];
        double R1 = (m1 == A.length) ? Integer.MAX_VALUE : A[m1];
        double L2 = (m2 == 0) ? Integer.MIN_VALUE : B[m2 - 1];
        double R2 = (m2 == B.length) ? Integer.MAX_VALUE : B[m2];

        // 如果L1 > R2,说明m1要少拿点,往左移
        if (L1 > R2) {
            right = m1 - 1;
            // 如果R1 < L2, m1要多拿点, 往右移
        } else if (R1 < L2) {
            left = m1 + 1;
        } else {
            // 判断奇偶
            if (len % 2 == 0) {
                double leftNum = (L1 > L2) ? L1 : L2;
                double rightNum = (R1 < R2) ? R1 : R2;
                return (leftNum + rightNum) / 2.0;
            } else {
                return (R1 < R2) ? R1 : R2;
            }
        }
    }
    return -1;
}
```

