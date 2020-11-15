# 题目地址

https://www.lintcode.com/problem/fibonacci/



# 题目描述

Find the *N*th number in Fibonacci sequence.

A Fibonacci sequence is defined as follow:

- The first two numbers are 0 and 1.
- The *i* th number is the sum of *i*-1 th number and *i*-2 th number.

The first ten numbers in Fibonacci sequence is:

```
0, 1, 1, 2, 3, 5, 8, 13, 21, 34 ...
```

### Example

```
Example 1:
	Input:  1
	Output: 0
	
	Explanation: 
	return the first number in  Fibonacci sequence .

Example 2:
	Input:  2
	Output: 1
	
	Explanation: 
	return the second number in  Fibonacci sequence .
```

### Notice

The *N*th fibonacci number won't exceed the max value of signed 32-bit integer in the test cases.





# 思路

经典递归题

会使用很大的空间,容易出现stackOverflow

纯递归(<font color = blue>从后往前</font>)的时间复杂度O(2<sup>n</sup>), 空间复杂度O(1)(<font color = red>因为压根没有任何额外空间</font>)



### 递推

此处需使用的是递推思想

仅需O(n)的时间复杂度和O(1)的空间复杂度

### 尾递归

尾递归是指递归函数中,递归调用是整个函数中最后的语句,且它的返回值不是表达式的一部分

函数可以不用等子函数执行完,直接自行销毁,不再占用内存, <font color = blue>空间复杂度从O(n)降为O(1)</font>

<font color = red>如果是纯递归的话,虽然是整个函数中最后一句,但它的返回值是表达式的一部分,所以上层函数不会自动销毁,从而stackOverflow</font>



# 题解

### 递推

时间复杂度O(n), 空间复杂度O(1)

```java
public int fibonacci(int n) {
    // write your code here
    if (n == 1) { // n == 1 和 n == 2 直接给出
        return 0;
    }

    if (n == 2) {
        return 1;
    }

    int a = 0; // a,b代表最初的第一项和第二项
    int b = 1;


    for (int i = 1; i < n - 1; i++) { // 每次都让a,b保存最近的两项,不用在意之前的
        int c = a + b;
        a = b;
        b = c;
    }

    return b;
}
```



### 尾递归

时间复杂度O(n), 空间复杂度O(1)

```java
public int fibonacci(int n) {
    // 1. 定义递归
    return fib(0, 1, n); 
}

private int fib(int a, int b, int n) {
    // 3. 递归出口
    if (n == 1) {
        return a;
    }
    // 2. 递归拆分
    return fib(b, a + b, n - 1);
}
```



