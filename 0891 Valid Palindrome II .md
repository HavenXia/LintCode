# 题目地址

https://www.lintcode.com/problem/valid-palindrome-ii



# 题目描述

Given a non-empty string `s`, you may delete at most one character. Judge whether you can make it a palindrome.

### Example

**Example 1:**

```
Input: s = "aba"
Output: true
Explanation: Originally a palindrome.
```

**Example 2:**

```
Input: s = "abca"
Output: true
Explanation: Delete 'b' or 'c'.
```

**Example 3:**

```
Input: s = "abc"
Output: false
Explanation: Deleting any letter can not make it a palindrome.
```

### Notice

1. The string will only contain lowercase characters.
2. The maximum length of the string is 50000.



# 思路

<font color = blue>注意: </font>

<font color = blue>此题不能把`isPalindrome`写成一个(String str, int index)的函数,这样加上index的遍历,O(n<sup>2</sup>)的时间复杂度,会直接rum too much time</font>

因此需要将他们组合成一个只有O(n)复杂度的 `isPalindrome` 函数

### 贪心算法

此处使用贪心算法

即在找到第一对不想等的char的时候先不退出,对<font color = red>内部`(left + 1, right)` 和 `(left, right - 1)` 进行判定</font>

在找到左右不相等的left和right index后

```java
return isPalindrome(s, left + 1, right) || isPalindrome(s, left, right - 1);
```





# 题解

### 贪心算法

时间复杂度O(n), 空间复杂度O(1)

```java
public boolean validPalindrome(String s) {
    // Write your code here

    if (s == null) { // 首先是null case
        return false;
    }


    if (isPalindrome(s, 0, s.length() - 1)) { // 直接判断全string是不是回文串
        return true;
    }

    int left = 0, right = s.length() - 1; // 用相同的办法找出第一个不等的left和right
    while (left < right && s.charAt(left) == s.charAt(right)) {
        left++;
        right--;
    }
	// 尝试舍去左侧和右侧得到不同的结果
    return isPalindrome(s, left + 1, right) || isPalindrome(s, left, right - 1);
}
        
private boolean isPalindrome(String str, int left, int right){

    while (left < right && str.charAt(left) == str.charAt(right)) {

        left++;
        right--;
    }

    return left >= right;
}
```

<font color = blue>但是这样的方法实在是code reuse太多</font>

因此可以通过开一个新class的办法来解决,设置一个pair存储left和right的值

```java
class Pair {
    int left, right;
    public Pair(int left, int right) {
        this.left = left;
        this.right = right;
    }
}
```

设置一个findDifference程序来查找最后不等的left和right

<font color = red>如果出来是left >= right, 那就正好是 `isPalindrome` 函数</font>

```java
public boolean validPalindrome(String s) {
    
   if (s == null) { // 首先是null case
        return false;
    }
    
   if (isPalindrome(s, 0, s.length() - 1)) { // 直接判断全string是不是回文串
       return true;
   }
    
   Pair pair = findDifference(s, 0, s.length() - 1);
   // 尝试舍去左侧和右侧得到不同的结果
   return isPalindrome(s, pair.left + 1, pair.right) || isPalindrome(s, pair.left, pair.right - 1);
}


// 相同方法查找第一个不相等的left和right index, 并以Pair返回
private Pair findDifference(String str, int left, int right) {
    while (left < right && str.charAt(left) == str.charAt(right)) {
        left++;
        right--;
    }
    return new Pair(left,right);
}

private boolean isPalindrome(String s, int left, int right){
    Pair pair = findDifference(s, left, right); // create一个pair object
    return pair.left >= pair.right; // 如果left >= right 那必然是走到头了
}
```

