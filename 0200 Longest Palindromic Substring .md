# 题目地址

https://www.lintcode.com/problem/longest-palindromic-substring



# 题目描述

Given a string `S`, find the longest palindromic substring in `S`. You may assume that the maximum length of `S` is 1000, and there exists one unique longest palindromic substring.

### Example

**Example 1:**

```
Input:"abcdzdcab"
Output:"cdzdc"
```

**Example 2:**

```
Input:"aba"
Output:"aba"
```

### Challenge

O(n2) time is acceptable. Can you do it in O(n) time.



# 思路

一个长度为n的字符串的substring数量级是O(n^2^)

<font color = blue>因为substring必然是连续的,一共有n + (n-1) + (n-2) + (n-3) + ....+ 1 个substring</font>

而Subsequence(<font color = blue>子序列</font>)可以是不连续的,一共可以有2^n^ -1 个

暴力解法需要O(n^3^)的复杂度,不满足O(n^2^)的要求

在检测 `xabcbac` 时,对于 `abcba` 和 `bcb` 会存在重复检测,因此可以选择从中间的c开始向两边检测.

+ 对于 `xabcbac` ,起初让双指针指向`c`然后向两边检测
+ 对于 `xabbac` ,起初让双指针指向 `b` 和 `b` 即可

<font color = red>尽可能的避免class里面的全局变量</font>,因此在本题中 `longest` 需要在外部比较 





#### 双指针检测回文数

```java
boolean isPalidrome(String s, int left, int right){
    while (left < right && s.charAt(left) == s.charAt(right)){ //确保回文数最终left会大于等于right
        left++;
        right--;
    }
    return left >= right; //一旦不是回文数,left必定小于right
}
```





# 关键点

+ 背向双指针



# 题解

### 中心点枚举法 

时间复杂度O(n<sup>2</sup>),空间复杂度O(1)

```java
public String longestPalindrome(String s) {
    // write your code here
    if (s == null){  // 首先考虑null case
        return s;
    }

    String longest = "";
    for (int i = 0; i < s.length(); i++){ // 对每个位置都要进行一遍 O(n)

        String oddPalindromeString = getPalindromeString(s, i, i); 
        if (longest.length() < oddPalindromeString.length()){ // 寻找最大值
            longest = oddPalindromeString;
        }

        String evenPalindromeString = getPalindromeString(s, i, i + 1);
        if (longest.length() < evenPalindromeString.length()){
            longest = evenPalindromeString;
        }
    }

    return longest;
}

// 对于odd, 从(i,i)向两边遍历,left必然会-1,即使在左右边界也没问题
// 对于even,从(i,i + 1)向两边遍历,首先str.substring(i,i)得到“”,左边界没有问题
// 对于even的右边界,可能出现str.substring(length,length)的情况,此时倚仗substring的特性,依旧得到“”
private String getPalindromeString(String str, int left, int right){ //O(n)
    while (left >= 0 && right <= str.length() - 1 && str.charAt(left) == str.charAt(right)){
        left--; 
        right++;
    }
    return str.substring(left + 1, right);
}
```

此外,java的substring有一个特点,当`str.substring(left,right)`时,如果left和right相等,得到的是空字符串`""`

<font color = red>并且当 `left = right = str.length` 时依旧能够运行</font> 

凭此特点才能不用检测**evenPalidromString下的最后一个index可能导致的IndexOutOfBound问题**

比如 `"abc"` 中i = 2时, left = 2, right = 3, 此时` "abc".substring(3,3)` 依旧是空字符串 `""`





### 基于动态规划题解

还没有学到,之后回来解

