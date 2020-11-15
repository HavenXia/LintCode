# 题目地址

https://www.lintcode.com/problem/wildcard-matching



# 题目描述


Implement wildcard pattern matching with support for `'?'` and `'*'`.

- `'?'` Matches any single character.
- `'*'` Matches any sequence of characters (including the empty sequence).

The matching should cover the entire input string (not partial).

### Example

**Example 1**

```plain
Input:
"aa"
"a"
Output: false
```

**Example 2**

```plain
Input:
"aa"
"aa"
Output: true
```

**Example 3**

```plain
Input:
"aaa"
"aa"
Output: false
```

**Example 4**

```plain
Input:
"aa"
"*"
Output: true
Explanation: '*' can replace any string
```

**Example 5**

```plain
Input:
"aa"
"a*"
Output: true
```

**Example 6**

```plain
Input:
"ab"
"?*"
Output: true
Explanation: '?' -> 'a' '*' -> 'b'
```

**Example 7**

```plain
Input:
"aab"
"c*a*b"
Output: false
```

### Notice

1<=|s|, |p| <= 1000
It is guaranteed that `𝑠` only contains lowercase Latin letters and `p` contains lowercase Latin letters , `?` and `*`



# 思路

本题依旧是遍历DFS改memoSearch

`dfs(String s, String p, int sIndex, int pIndex, boolean[][]memo, boolean[][] visited)`

+ 每次判断是否 **`p.charAt(pIndex)`等于***, 因为这里?只能代表一个字母, 所以并不需要特意检测

  + 如果 **`p.charAt(pIndex) != '*'`**, <font color = grape>那么就返回 **`match(sChar,pChar) && memoSearch(s,p,sIndex+1,pIndex+1,memo,visited)`**</font>

    注意: 这里已经运用上了分治/dp的思想,现在的boolean是等于当前的char相同**且**后续的也返回true的情况下才是true

  + 如果 **`p.charAt(pIndex) == '*'`**, <font color = grape>就返回 **`memoSearch(s,p,sIndex+1,pIndex,memo,visited)||memoSearch(s,p,sIndex,pIndex+1,memo,visited)`**</font> 

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
    
    if (pChar == '*') {
        match = isMatchHelper(s, sIndex, p, pIndex + 1, memo, visited) ||
            isMatchHelper(s, sIndex + 1, p, pIndex, memo, visited);
    } else {
        match = charMatch(sChar, pChar) &&
            isMatchHelper(s, sIndex + 1, p, pIndex + 1, memo, visited);
    }
    ```

    

    <font color = red>因此可能会出现一个*直接代替所有剩下的sequence的情况, 所以在递归出口要讨论</font>

+ 递归出口

  + 如果p走完了, 那么s必须也走完了
  + 如果s走完了, **那么p剩下的必须都是* **

#### memorizedSearch修改

这样的dfs必然会存在重复, 比如s="abcd", p="a***"的时候,    `memo[3][3]` 可能出现在先`memo[2][2]` 到`memo[2][3]` 后, 也可能出现在`memo[2][2]`到`memo[3][2]`

所以每次将结果存到memo中, **并且将visited中对应改成true**



# 题解

时间复杂度O(m * n), 空间复杂度O(m * n)

```java
public boolean isMatch(String s, String p) {
    // write your code here
    if (s == null || p == null) {
        return false;
    }

    boolean[][] memo = new boolean[s.length()][p.length()];
    boolean[][] visited = new boolean[s.length()][p.length()];
    return isMatchHelper(s, 0, p, 0, memo, visited);
}

private boolean isMatchHelper(String s, int sIndex,
                              String p, int pIndex,
                              boolean[][] memo,
                              boolean[][] visited) {

    if (pIndex == p.length()) {
        return sIndex == s.length();
    }
    if (sIndex == s.length()) {
        return allStar(p, pIndex);
    }

    if (visited[sIndex][pIndex]) {
        return memo[sIndex][pIndex];
    }

    char sChar = s.charAt(sIndex);
    char pChar = p.charAt(pIndex);
    boolean match;

    if (pChar == '*') {
        match = isMatchHelper(s, sIndex, p, pIndex + 1, memo, visited) ||
            isMatchHelper(s, sIndex + 1, p, pIndex, memo, visited);
    } else {
        match = charMatch(sChar, pChar) &&
            isMatchHelper(s, sIndex + 1, p, pIndex + 1, memo, visited);
    }

    visited[sIndex][pIndex] = true;
    memo[sIndex][pIndex] = match;
    return match;
}

private boolean charMatch(char sChar, char pChar) {
    return (sChar == pChar || pChar == '?');
}

private boolean allStar(String p, int pIndex) {
    for (int i = pIndex; i < p.length(); i++) {
        if (p.charAt(i) != '*') {
            return false;
        }
    }
    return true;
}
```

