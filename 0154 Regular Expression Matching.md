# 题目地址

https://www.lintcode.com/problem/regular-expression-matching



# 题目描述

Implement regular expression matching with support for `'.'` and `'*'`.

```
'.' Matches any single character.
'*' Matches zero or more of the preceding element.
The matching should cover the entire input string (not partial).
The function prototype should be:

bool isMatch(string s, string p)
```



isMatch("aa","a") → false

isMatch("aa","aa") → true

isMatch("aaa","aa") → false

isMatch("aa", "a*") → true

isMatch("aa", ".*") → true

isMatch("ab", ".*") → true

isMatch("aab", "c*a*b") → true

### Example

**Example 1:**

```
Input："aa"，"a"
Output：false
Explanation：
unable to match
```

**Example 2:**

```
Input："aa"，"a*"
Output：true
Explanation：
'*' can repeat a
```



# 思路

本题依旧是DFS改memoSearch

`dfs(String s, String p, int sIndex, int pIndex, boolean[][]memo, boolean[][] visited)`

+ 每次判断是否 **`p.charAt(pIndex)`等于***, 因为这里.只能代表一个字母, 所以并不需要特意检测

  + 如果 **`pIndex == len - 1 || p.charAt(pIndex+1) != '*'`**,  如果pIndex已经是最后一位也就直接match就好了

    <font color = grape>那么就返回 **`(match(sChar,pChar) && memoSearch(s,p,sIndex+1,pIndex+1,memo,visited,sChar))`**</font>

    注意: 这里已经运用上了分治/dp的思想,现在的boolean

    + 等于当前匹配上了,并且之后所有的也都匹配上了

  + 如果 **`p.charAt(pIndex+1) == '*'`**, 分为两种情况

    + <font color = grape>囊括当前的sChar,返回 **`(match(sChar,pChar) && memoSearch(s,p,sIndex+1,pIndex+1,memo,visited))`**</font> 
    + 直接理解为empty, 返回 **`memoSearch(s,p,sIndex,pIndex+1,memo, visited)`**

    注意: 这里pIndex+1是因为*也可以代表empty string,**可以作为每次当前 * 所代替的sequence的结尾**

     **而sIndex+1相当于先把当前的char囊括到 * 的sequence里, 然后继续看下一位**

  ```java
  if (pIndex == p.length()) {
      return sIndex == s.length();
  }
  if (sIndex == s.length()) {
      return allStar(p, pIndex);
  }
  char sChar = s.charAt(sIndex);
  char pChar = p.charAt(pIndex);
  boolean match;
  
  if (pIndex + 1 < p.length() && p.charAt(pIndex + 1) == '*') {    	 //如果为'*'，有两种方案
      match = isMatchHelper(s, sIndex, p, pIndex + 2, memo, visited) ||                //'*'不去匹配字符
          charMatch(sChar, pChar) && isMatchHelper(s, sIndex + 1, p, pIndex, memo, visited);  //'*'重复前面一个字符去匹配s
  } else {
      match = charMatch(sChar, pChar) && 		//如果当前两字符匹配
          isMatchHelper(s, sIndex + 1, p, pIndex + 1, memo, visited); //继续下一个字符匹配
  }
  ```

+ 递归出口
  + 如果p走完了, 那么s必须也走完了
  + 如果s走完了, **那么p剩下的必须都是* **

  #### memorizedSearch修改

  这样的dfs必然会存在重复, 比如s="abcd", p="a***"的时候,    `memo[3][3]` 可能出现在先`memo[2][2]` 到`memo[2][3]` 后, 也可能出现在`memo[2][2]`到`memo[3][2]`

  所以每次将结果存到memo中, **并且将visited中对应改成true**





# 题解

时间复杂度O(mn), 空间复杂度O(mn)

```java
public boolean isMatch(String s, String p) {
    // write your code here
    if (s == null || p == null) {
        return false;
    }

    boolean[][] memo = new boolean[s.length()][p.length()];			//记忆搜索结果
    boolean[][] visited = new boolean[s.length()][p.length()];		//标记是否访问

    return isMatchHelper(s, 0, p, 0, memo, visited);
}

private boolean isMatchHelper(String s, int sIndex,
                              String p, int pIndex,
                              boolean[][] memo,
                              boolean[][] visited) {
    // "" == ""
    if (pIndex == p.length()) {       //如果p已经匹配完毕
        return sIndex == s.length();	//根据s是否匹配完毕即可
    }

    if (sIndex == s.length()) {		//如果s匹配完毕
        return isEmpty(p, pIndex);
    }

    if (visited[sIndex][pIndex]) {
        return memo[sIndex][pIndex];
    }

    char sChar = s.charAt(sIndex);
    char pChar = p.charAt(pIndex);
    boolean match;

    // consider a* as a bundle
    if (pIndex + 1 < p.length() && p.charAt(pIndex + 1) == '*') {    	 //如果为'*'，有两种方案
        match = isMatchHelper(s, sIndex, p, pIndex + 2, memo, visited) ||                //'*'不去匹配字符
            charMatch(sChar, pChar) && isMatchHelper(s, sIndex + 1, p, pIndex, memo, visited);  //'*'重复前面一个字符去匹配s
    } else {
        match = charMatch(sChar, pChar) && 		//如果当前两字符匹配
            isMatchHelper(s, sIndex + 1, p, pIndex + 1, memo, visited); //继续下一个字符匹配
    }

    visited[sIndex][pIndex] = true;    //搜索完成就标记
    memo[sIndex][pIndex] = match;     //存储搜索结果
    return match;
}

private boolean charMatch(char sChar, char pChar) {		//判断两字符是否匹配
    return sChar == pChar || pChar == '.';
}

private boolean isEmpty(String p, int pIndex) {    //形如"x*x*"形式
    for (int i = pIndex; i < p.length(); i += 2) {
        if (i + 1 >= p.length() || p.charAt(i + 1) != '*') {   //如果不是'*'，无法匹配
            return false;
        }
    }
    return true;
}
```













