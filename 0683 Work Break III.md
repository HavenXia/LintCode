# 题目地址

https://www.lintcode.com/problem/word-break-iii



# 题目描述

Give a dictionary of words and a sentence with all whitespace removed, return the number of sentences you can form by inserting whitespaces to the sentence so that each word can be found in the dictionary.

### Example

**Example1**

```
Input:
"CatMat"
["Cat", "Mat", "Ca", "tM", "at", "C", "Dog", "og", "Do"]
Output: 3
Explanation:
we can form 3 sentences, as follows:
"CatMat" = "Cat" + "Mat"
"CatMat" = "Ca" + "tM" + "at"
"CatMat" = "C" + "at" + "Mat"
```

**Example1**

```
Input:
"a"
[]
Output: 
0
```

### Notice

Ignore case



# 思路

依旧是动态规划的思路

按照start和end的index, 把i,j分别作为start和end

+ State: `dp[i][j]` 代表index从i到j, **左开右闭的all combination(所有的分割方法)**

+ Initialize: <font color = grape>**每一个点都可以Initialize! 初始值为从i到j整个substring是否存在于set中,存在为1,反之为0**</font>

+ Function: 这里其实不需要用到i, 因为需要的答案是 **`dp[0][n]`**

  <font color = red>**这里不是自底向上也不是自顶向下的循环!**</font> 这里的循环需要考虑到overcounting的存在

  $dp[i][j]=\sum_{k=i+1}^{j-1} dp[i][k]\times dp[k][j]$

  注意这里是存在**OverCounting**的! `dp[k][j]`只能是initialize的值(即对于substring的判断存在), 而`dp[i][k]`则是all combination的值

+ Answer: 最后返回的值是`dp[0][n]`





# 题解

时间复杂度O(N<sup>2</sup>logn), **N<sup>2</sup>来自j和k的循环, logn来自string的hash set**

空间复杂度O(N<sup>2</sup>)

```java
public int wordBreak3(String s, Set<String> dict) {
    // Write your code here
    int n = s.length();
    String lowerS = s.toLowerCase();
    Set<String> lowerDict = new HashSet<String>();

    for(String str : dict) {
        lowerDict.add(str.toLowerCase());
    }

    // 左开右闭
    int[][] dp = new int[n][n + 1];
    for(int i = 0; i < n; i++){
        for(int j = i + 1; j <= n;j++){
            String sub = lowerS.substring(i, j);
            if(lowerDict.contains(sub)){
                dp[i][j] = 1;
            }
        }
    }

    for(int j = 1; j <= n; j++){
        for(int k = 1; k < j; k++){
            dp[0][j] += (dp[0][k] * dp[k][j]);

        }
    }
    return dp[0][n];
}
```

