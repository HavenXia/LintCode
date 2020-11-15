# 题目地址

https://www.lintcode.com/problem/word-break-ii



# 题目描述

Given a string s and a dictionary of words dict, add spaces in s to construct a sentence where each word is a valid dictionary word.

Return all such possible sentences.

### Example

**Example 1:**

```
Input："lintcode"，["de","ding","co","code","lint"]
Output：["lint code", "lint co de"]
Explanation：
insert a space is "lint code"，insert two spaces is "lint co de".
```

**Example 2:**

```
Input："a"，[]
Output：[]
Explanation：dict is null.
```



# 思路

非常类似[Work Break III](/Users/parallax/Documents/求职/LintCode/0683 Work Break III.md),但是相比之下这里更注重判断, 因为当存在的时候, 需要把结果存储到result里面

这里是以遍历法DFS再改**记忆化搜索**来完成的

+ 首先写出遍历法DFS

  <font color = red>注意: 此处dfs的result不是全局传递的result, 因为每一层都是根据下一层来构建result的!</font>

  + 对于递归, 每次先从1开始,取string的substring, 直到找到第一个存在于dict的substring

    **然后, 对于剩下的suffix部分, 直接进入下一轮dfs, 返回的是`List<String> suffixResults`**

    这里return的arraylist里面的就是一个个已经按格式写好的str + “ ” + string

    ```java
    if (dict.contains(s)) {
        results.add(s);
    }
    // 因为整个s已经在上面判断了, 所以这里i只要走到倒数第二位就行了, 还可以避免suffix为空的一些edge case
    for (int i = 1; i < s.length; i++) {
        String prefix = s.substring(0,i);
        if (!dict.contains(prefix)) {
            continue;
        }
        String suffix = s.substring(i);
        List<String> suffixResults = dfs(suffix, dict);
    
        if (suffixResults.length != 0) {
            for (string suffixResult : suffixResults) {
                results.add(word + " " + suffixResult);
            }
        }
    }
    return result;
    ```

    **递归出口: 如果s本身就存在于dict中, 直接加到result里, 然后再看s还能不能再分割**

+ 改进memorizedSearch

  **为什么要记忆化搜索?** <font color = grape>**比如”abcde“, 取abc为prefix的时候, 遍历会遇到之前取a为prefix时已经遍历过的cd**</font>

  非常简单, arguments加入一个**`Map<String, List<String>> memo`**

  每次进入dfs的时候, 先检测是否s已经在map里了, 是的话连results都不用创建, 直接返回就可以了

  每次return前, 把当前的results和s加入到map里(<font color = red> results为空也要put进去,这样比如取a时候只有bcde(在这个过程中,cde被判断并以empty result加入了memo), 那么取ab的时候, cde直接就是return empty了, 然后ab就也是empty result了</font>,接着就是去取abc了)





# 题解

存疑:时间复杂度O(n<sup>2</sup>), 空间复杂度O(n<sup>2</sup>)

```java
public List<String> wordBreak(String s, Set<String> wordDict) {
    // write your code here
    Map<String, List<String>> memo = new HashMap<String, List<String>>();
    return dfs(s, wordDict,memo);
}
private List<String> dfs(String s, Set<String> dict, Map<String, List<String>> memo) {

    if (memo.containsKey(s)) {
        return memo.get(s);
    }
    List<String> results = new ArrayList<String>();
    if (dict.contains(s)) {
        results.add(s);
    }
    for (int i = 1; i < s.length(); i++) {

        String prefix = s.substring(0,i);
        if (!dict.contains(prefix)) {
            continue;
        }
        String suffix = s.substring(i);
        List<String> suffixResults = dfs(suffix, dict, memo);

        if (suffixResults.size() != 0) {
            for (String suffixResult : suffixResults) {
                results.add(prefix + " " + suffixResult);
            }
        }
    }
    memo.put(s, results);
    return results;
}
```

