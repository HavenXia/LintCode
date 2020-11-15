# 题目地址

https://www.lintcode.com/problem/wood-cut



# 题目描述

Given n pieces of wood with length `L[i]` (integer array). Cut them into small pieces to guarantee you could have equal or more than k pieces with the same length. What is the longest length you can get from the n pieces of wood? Given L & k, return the maximum length of the small pieces.

### Example

**Example 1**

```plain
Input:
L = [232, 124, 456]
k = 7
Output: 114
Explanation: We can cut it into 7 pieces if any piece is 114cm long, however we can't cut it into 7 pieces if any piece is 115cm long.
```

**Example 2**

```plain
Input:
L = [1, 2, 3]
k = 7
Output: 0
Explanation: It is obvious we can't make it.
```

### Challenge

O(n log Len), where Len is the longest length of the wood.

### Notice

You couldn't cut wood into float length.

If you couldn't get >= *k* pieces, return `0`.



# 思路

题目的意思是数组里的是每根木头的长度, 要求切出k份相同长度的木头, <font color = blue>问切出来木头长度的最大值</font>

比如[232, 124, 456] , k = 7, 可以切成[232 / 144 = 2,124 / 114 = 1, 456 / 114 = 4],加起来正好为7

而114就是此时length的最大值

<font color = red>因此这道题我们二分的对象是da</font>

**用倍增法即可, length从1开始**

### 找start和end(方法1和方法2都用long声明以防溢出)

#### 方法一

```java
int length = 1;
while (getWoodNumber(L,length) >= k) {
    length *= 2;
}
```

<font color = blue>**这样可以直接找出二分的不能>= k的最小值**</font> 

然后start = legnth / 2, end = length, <font color = blue>时间复杂度O(nlog(length/2)</font>

#### 方法二

```java
long max = 0;
for (int i = 0; i < L.length; i++) {
    max = Math.max(max, L[i]);
}
```

Start = 0, end = (long)max + 1(<font color = red>+1是为了确保end是不满足条件的,符合之后二分时end只在不符合条件时变化</font>)

避免出现[10000,10001,10002,10003], k = 1时end会满足的情况

<font color = blue>这样做长度更长,但是找start和end本身的时间复杂度O(n)</font> 

**<font color = red>改进方案: 不需要对start和end用long声明</font>**

+ 但是这样的话end=max, 在出循环后,<font color = red>就不能直接return start了, 因为有可能从最开始end就是符合条件的最大值</font>
+ 在这种情况下:
  + 可以在设置完end后直接检测下 getWoodNumber(L, end) 是否>=k, 如果是,直接返回end就可以了
  + 或者在出循环后先对end检测下, 不满足的情况下再返回start
+ <font color = red>**但是!!! getWoodNumber 函数的返回值一定是long的, 因为很容易出现woodNumber越界**</font>

### 对答案集二分 

+ 如果 getWoodNumber(L,mid) 大于等于k, 则start = mid
+ 反之则end = mid

这部分时间复杂度是O(nlog(max+1)), 或者可以说是O(nlog(length/2))

<font color = red>此处的length和max其实差距不大,但是一般是max更小一些</font>



# 题解

时间复杂度O(nlog(max)), 空间复杂度O(1)

```java
public int woodCut(int[] L, int k) {
    // null case
    if (L == null || L.length == 0) { 
        return 0;
    }

    if (getWoodNumber(L, 1) < k) {
        return 0;
    }
    // 10 - 14 行为方法一
	long length = 1;
    while (getWoodNumber(L, length) >= k) {
        length *= 2;
    }
	// 15 - 19 行为方法二
    long max = 0;
    for (int i = 0; i < L.length; i++) {
        max = Math.max(max, L[i]);
    }
	// 如果用方法一,start = length / 2, end = length
    long start = 1, end = (long) max + 1;
    
    while (start + 1 < end) {
        // mid也得是long
        long mid = start + (end - start) / 2;
        if (getWoodNumber(L, mid) >= k) {
            start = mid;
        } else {
            end = mid;
        }
    }
	// 恢复为int
    return (int)start;
}

// 这一部分时间复杂度为O(n)
// number也得用long
private long getWoodNumber(int[] L, long length) {
    long number = 0;
    for (int i = 0; i < L.length; i++) {
        number += L[i] / length;
    }
    return number;
}
```



### 改进方法

只有getWoodNumber这个函数需要long类型

start和end 都可以用int

```java
public int woodCut(int[] L, int k) {
        // write your code here
        if (L == null || L.length == 0) {
            return 0;
        }       
    	// 检测是否切不出来
        if (getWoodNumber(L, 1) < k) {
            return 0;
        }
    	// 用int即可,max不会超过int的最大值
        int max = Integer.MIN_VALUE;
        for (int i = 0; i < L.length; i++) {
            max = Math.max(L[i],max);
        }
        
        int start = 1, end = max;
        
        while (start + 1 < end) {
            int mid = start + (end - start) / 2;
            
            if (getWoodNumber(L,mid) >= k) {
                start = mid;
            } else {
                end = mid;
            }
        }
        // 因此出循环需要检测一下是否end也成立,其次再返回start
        if (getWoodNumber(L, end) >= k) {
            return end;
        }
        return start;
    }
    // 这个函数一定得用long,只要元素够多, 总计数很可能超过int上限 
    private long getWoodNumber(int[] L, int len) {
        
        long woodNumber = 0;
        for (int i = 0; i < L.length; i++) {
            woodNumber += L[i] / len;
        }
        return woodNumber;
    }
```

