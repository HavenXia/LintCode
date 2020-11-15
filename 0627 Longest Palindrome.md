# 题目地址

https://www.lintcode.com/problem/longest-palindrome



# 题目描述

Given a string which consists of lowercase or uppercase letters, find the length of the longest palindromes that can be built with those letters.

This is case sensitive, for example `"Aa"` is not considered a palindrome here.

### Example

**Example 1:**

```
Input : s = "abccccdd"
Output : 7
Explanation :
One longest palindrome that can be built is "dccaccd", whose length is `7`.
```

### Notice

Assume the length of given string will not exceed `100000`.





# 思路

计算用给定字符串中字符能拼出的最长回文字符

<font color = red>特点在于只能存在一个奇数,剩下的所有char都必须是偶数位</font>

在计算总和时用一个hasOdd参数来区分是否要把当前的奇数变为偶数





# 题解

时间复杂度O(n),空间复杂度O(n),<font color = blue>空间复杂度来自HashMap</font>

```java
public int longestPalindrome(String s) {
    // write your code here

    if (s == null || s.equals("")) {
        return 0;
    }

    Map<Character,Integer> charMap = new HashMap<Character,Integer>();

    for (char letter: s.toCharArray()){ // 用hashMap存储每个letter和出现的次数
        if (!charMap.containsKey(letter)){
            charMap.put(letter,1);
        }else{
            charMap.put(letter, charMap.get(letter) + 1);
        }
    }

    int longest = 0;
    boolean hasOdd = false; // 设置一个boolean确保区分是否要把奇数变为偶数

    for (char key: charMap.keySet()) {

        if (hasOdd) { 
            longest += (charMap.get(key) / 2) * 2; // 如果已经有odd了,用整除在乘回来
        }else{
            longest += charMap.get(key); // 否则直接增加
        }

        if (charMap.get(key) % 2 == 1){
            hasOdd = true;
        }
    }

    return longest;
}
```

