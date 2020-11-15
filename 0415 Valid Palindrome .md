# 题目地址

https://www.lintcode.com/problem/valid-palindrome



# 题目描述

Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.

### Example

**Example 1:**

```
Input: "A man, a plan, a canal: Panama"
Output: true
Explanation: "amanaplanacanalpanama"
```

**Example 2:**

```
Input: "race a car"
Output: false
Explanation: "raceacar"
```

### Challenge

O(n) time without extra memory.

### Notice

Have you consider that the string might be empty? This is a good question to ask during an interview.

For the purpose of this problem, we define empty string as valid palindrome.



# 思路

使用双向双指针法

题目说明only alphanumeric,即只有数字和字母是可以保留下来的

因此在遍历时要判断当前的char,如果不是则在走一步

<font color = red>不能用str.replaceAll(regex,str), 因为这一步的时间复杂度可能很大</font>



# 题解

时间复杂度O(n),空间复杂度O(1)

```java
public boolean isPalindrome(String s) {

    if (s == null) { // 首先是null case
        return false;
    }

    int left = 0, right = s.length() - 1; 
    while (left < right) { // 设置循环条件

        while (left < right && !isValid(s.charAt(left))) { // 如果不写left < right可能会直接OutOfBound
            left++;
        }

        while (left < right && !isValid(s.charAt(right))) { // 同样要写,防止上一步left已经超过right
            right--;
        }

        if (left < right && s.toLowerCase().charAt(left) != s.toLowerCase().charAt(right)) { // 检测回文
            return false; 
        }
        left++;
        right--;
    }

    return true; // 能走到这一步意味着双指针相碰且没有false
} 

private boolean isValid(char c) {
    return Character.isLetter(c) || Character.isDigit(c);
}
```

+ 使用 `Character.isLetter()` 和 `Character.isDigit()` 构成检测函数
+ `left < right` 是每一步都要检测的步骤,否则很容易出现 `OutOfBoundException` 

