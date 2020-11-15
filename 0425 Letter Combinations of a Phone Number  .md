# 题目地址

https://www.lintcode.com/problem/letter-combinations-of-a-phone-number



# 题目描述

Given a digit string excluded `0` and `1`, return all possible letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given below.

![image-20201007223515865](https://live.staticflickr.com/65535/50434641962_8a9c6b92c6_o.png)

### **Example**

**Example 1:**

```
Input: "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"]
Explanation: 
'2' could be 'a', 'b' or 'c'
'3' could be 'd', 'e' or 'f'
```

**Example 2:**

```
Input: "5"
Output: ["j", "k", "l"]
```





# 思路

采用类似[Subsets](/Users/parallax/Documents/求职/LintCode/0017 SubSets.md)的思路来做

本质是DFS, 每一个char对应一个char的array

然后一层一层进入

<font color = red>注意: 中执教天就是index走到了length长度</font>



# 题解

时间复杂度**O(3<sup>n</sup>·4<sup>m</sup>),** where `N` is the number of digits in the input that maps to 3 letters (*e.g.* `2, 3, 4, 5, 6, 8`) and `M` is the number of digits in the input that maps to 4 letters (

空间复杂度**O(3<sup>n</sup>·4<sup>m</sup>),** 来自返回数组

```java
public List<String> letterCombinations(String digits) {
    // write your code here

    if (digits == null || digits.length() == 0) {
        return new ArrayList<String>();
    }

    char [] digit = digits.toCharArray();
    Map <Character, char[]> map = new HashMap<Character, char[]>();
    map.put('2', new char[]{'a','b','c'});
    map.put('3', new char[]{'d','e','f'});
    map.put('4', new char[]{'g','h','i'});
    map.put('5', new char[]{'j','k','l'});
    map.put('6', new char[]{'m','n','o'});
    map.put('7', new char[]{'p','q','r','s'});
    map.put('8', new char[]{'t','u','v'});
    map.put('9', new char[]{'w','x','y','z'});
    List <String> result = new ArrayList<String>();
    char[] word = new char[digit.length];

    traverse(result, 0, word, digit, map);
    return result;
}

private void traverse(List <String> result, int index, char[] word, char[] digit, Map <Character, char[]> map) {

    if (index == word.length) {
        result.add(new String(word));
        return;
    }

    char ch = digit[index];

    for (int j = 0; j < map.get(ch).length; j++) {
        word[index] = map.get(ch)[j];
        traverse(result, index + 1, word, digit, map);
        word[index] = 0;
    }
}
```

