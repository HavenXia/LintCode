# 题目地址

https://www.lintcode.com/problem/alien-dictionary



# 题目描述

There is a new alien language which uses the latin alphabet. However, the order among letters are unknown to you. You receive a list of **non-empty** words from the dictionary, where words are **sorted lexicographically by the rules of this new language**. Derive the order of letters in this language.

### Example

**Example 1:**

```
Input：["wrt","wrf","er","ett","rftt"]
Output："wertf"
Explanation：
from "wrt"and"wrf" ,we can get 't'<'f'
from "wrt"and"er" ,we can get 'w'<'e'
from "er"and"ett" ,we can get 'r'<'t'
from "ett"and"rftt" ,we can get 'e'<'r'
So return "wertf"
```

**Example 2:**

```
Input：["z","x"]
Output："zx"
Explanation：
from "z" and "x"，we can get 'z' < 'x'
So return "zx"
```

### Notice

1. You may assume all letters are in lowercase.
2. The dictionary is invalid, if a is prefix of b and b is appear before a.
3. If the order is invalid, return an empty string.
4. There may be multiple valid order of letters, return the smallest in normal lexicographical order



# 思路

本题理解:

+ 字典序比较的时候是自动把两个string补齐到相同长度在比较的, <font color = blue>如"ab  "和"abc", 'c'是和' '所比较, 字母一定是大于blank的</font>

+ 如果出现 'a'<'b' 但是input给出'b' 'a', 那代表input是invalid的,<font color = blue>例如"ab"在"a "前面时,  因为'b'是不可能小于一个blank的</font>

+ 如果是invalid的输入,返回一个空string
+ 可能出现多个有效排序, 如 "abwertf" 和 "bawertf", 此时需要**普通字典序**下最小的序列, <font color = blue>可以理解为应选择先a后b</font>

因此:

+ 首先需要通过一个函数建图, 把每个char之间的关系用一个 `HashMap<Character, Set<Character>>` 来记录, key和value分别是**char和其后续节点**. <font color = blue>建图时, 先遍历所有的word的每一个char, 为每个字母初始化一个Set</font>

+ 接着遍历每个word和其下一个word, 比较每一个index对应的char, 如果相等就看下一个, 如果不想等就把后一个char加入前一个char的**后续节点Set**中,并break出循环. <font color = blue>出循环后如果此时index就是min(两个word.length), 说明两个word完全相等</font>

  + 如果第一个word.length > 第二个, 那就是invalid了, 直接return一个null Map
  + 反之则是正常的, blank < 任意char, 无需改变

+ 接着用topo Sorting函数进行排序, 首先遍历graph, 用一个 `HashMap<Character, Integer>` 统计入度

  + <font color = red>**此处必须用PriorityQueue来实现Queue, 因为要确保多个入度为0的char时按照自然字典序, Priority会自动poll出当前最小的char, 满足此处要求**</font>

  + 用StringBuilder完成string的建立, Queue中拿出一个char，去掉这个char的所有后续char，其他char的相应的入度 - 1

  + 上面比较word.length的invalid只是有blank出现时的特例

    如果遇到<font color = blue>b中有a,a中有b的invalid时, 需要在toposorting的最后比较一下result长度和char个数, 如果不想等,那必然是出现了invalid的情况,导致b和a的入度永远不等于0, 无法进入queue</font>





# 题解

时间复杂度O(n +m), 来自BFS

空间复杂度O(n + m), 来自graph的key和value

```java
public String alienOrder(String[] words) {
    // 无需null case, 题目说明non-empty
    Map<Character, Set<Character>> graph = getGraph(words);
    // 出现了invalid的blank在char前面
    if (graph == null) {
        return "";
    }
    return topoSorting(graph);
}

// 建图
private Map<Character, Set<Character>> getGraph(String[] words) {

    Map<Character, Set<Character>> graph = new HashMap<Character, Set<Character>>();
    // 初始化这些set
    for (String word : words) {
        for (int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            // 如果还没有记录过就初始化一下
            if (!graph.containsKey(c)) {
                graph.put(c, new HashSet<Character>());
            }
        }
    }

    // 遍历完成后续节点set
    int index;
    for (int i = 0; i < words.length - 1; i++) {
        for ( index = 0; index < words[i].length() && index < words[i + 1].length(); index++) {
            // 如果有不同的就加入后续set并break
            if (words[i].charAt(index) != words[i + 1].charAt(index)) {
                graph.get(words[i].charAt(index)).add(words[i + 1].charAt(index));
                break;
            }
        }
        // 如果出循环的时候index 等于短的word长度时,就代表全部走完了
        // 如果第一个word.length > 第二个, 那就是invalid了, 直接return一个null Map
        // 反之则是正常的, blank < 任意char, 无需改变
        if (index == Math.min(words[i].length(), words[i + 1].length())) {
            if (words[i].length() >= words[i + 1].length()) {
                return null;
            }
        }
    }
    // 建图完毕
    return graph;
}

// 拓扑排序
private String topoSorting(Map<Character, Set<Character>> graph) {

    // 统计入度
    Map<Character, Integer> indegree = new HashMap<Character, Integer>();
    // 必须分两次,确保每个char都进入indegree
    for (Character u : graph.keySet()) {
        indegree.put(u, 0);
    }

    for (Character u : graph.keySet()) {
        for (Character v : graph.get(u)) {
            indegree.put(v, indegree.get(v) + 1);
        }
    }     
    // 把入度为0的char加入queue
    Queue<Character> queue = new PriorityQueue<Character>();
    for (char c : indegree.keySet()) {
        if (indegree.get(c) == 0) {
            queue.offer(c);
        }
    }

    // 使用StringBuilder来修改string
    StringBuilder sb = new StringBuilder();
    // BFS
    while (!queue.isEmpty()) {
        char now = queue.poll();
        sb.append(now);
        for (char next : graph.get(now)) {
            indegree.put(next, indegree.get(next) - 1);
            if (indegree.get(next) == 0) {
                queue.offer(next);
            }
        }
    }

    // 当b中有a,a中有b的invalid时, 需要在toposorting的最后比较一下string长度和char个数
    // 如果不想等,那必然是出现了invalid的情况,导致b和a的入度永远不等于0, 无法进入queue
    if (sb.length() != indegree.size()) {
        return "";
    }
    return sb.toString();
}
```

