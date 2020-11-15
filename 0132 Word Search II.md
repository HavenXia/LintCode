# 题目地址

https://www.lintcode.com/problem/word-search-ii



# 题目描述

Given a matrix of lower alphabets and a dictionary. Find all words in the dictionary that can be found in the matrix. A word can start from any position in the matrix and go left/right/up/down to the adjacent position. One character only be used once in one word. No same word in dictionary

### Example

**Example 1:**

```
Input：["doaf","agai","dcan"]，["dog","dad","dgdg","can","again"]
Output：["again","can","dad","dog"]
Explanation：
  d o a f
  a g a i
  d c a n
search in Matrix，so return ["again","can","dad","dog"].
```

**Example 2:**

```
Input：["a"]，["b"]
Output：[]
Explanation：
 a
search in Matrix，return [].
```

### Challenge

Using trie to implement your algorithm.





# 思路

这里不能像以前的海岛问题一样, 单纯的用一个visited, 每次走过就mark

不同的单词可能会走同一个字母

<font color = blue>注意: 每个point在一个单词里只能出现一次,但是不同point的char是可能相同的</font>

**使用HashMap**

用一个HashMap分别存储String 和 Boolean

+ 对于dict的每一个单词, 都把它<font color = red>除了本身以外的sequence当作一个prefix加入map,</font> 属性为 false

  比如对于 again, 把a, ag, aga, agai 加入map, 这样可以在每一步都检查是否直接退出(**其实就是剪枝**)

+ 接着对每个word, 也把它加入map, 属性为true

  <font color = red>这一步一定要后做!!!! 比如 no 是none的sequence, 但是也是单词, 所以覆盖</font>

+ 依旧构建一个Visited array, 但是这次的作用不是走过就mark

  <font color = red>而是在DFS的时候, 告诉更深的层已经走过了, 所以需要**backtrack**</font>



**在DFS函数里:**

+ 先检测下当前的testWord在不在map的key里, 如果不在, 直接return, 改掉最后一个char

+ 如果在map里, 查看一下是<font color = red>prefix 还是 word</font>, 是prefix就不变, 是word就加入result, <font color = red>但是不能return!! 比如no找到了还有none</font>

+ 进入for loop, 首先对 xy进行更改, 然后检测是否出界和<font color = red>是否visited</font>

  + 如果没出界也没有在之前访问过, 就<font color = red>让visited[new i] [new j] = true</font>, 以确保dfs不会走到
  + 进入DFS
  + 出来之后要back track, 修改visited的值为false!!!

  

  

  

  # 题解

  时间复杂度O(n * m * n * m), 最差每个点每次遍历都是

  空间复杂度同上

  ```java
  public List<String> wordSearchII(char[][] board, List<String> words) {
      // write your code here
  
      List<String> result = new ArrayList<String>();
      if (board == null || board.length == 0 || board[0].length == 0) {
          return result;
      }
  
      int[][] direction = new int[][]{{-1,0},{1,0},{0,1},{0,-1}};
      String testWord = "";
      boolean[][] visited = new boolean[board.length][board[0].length];
  
      Set<String> set = new HashSet<String>();
      Map<String,Boolean> map = new HashMap<String,Boolean> ();
  
      for (String word: words) {
          for (int k = 0; k < word.length() - 1; k++) {
              String prefix = word.substring(0, k + 1);
              if (!map.containsKey(prefix)) {
                  map.put(prefix, false);
              }
          }
          map.put(word, true);
      }
  
      for (int i = 0; i < board.length; i++) {
          for (int j = 0; j < board[0].length; j++) {
              visited[i][j] = true;
              traverse(i, j, direction, board, testWord, set, map, visited);
              visited[i][j] = false;
          }
      }
      return new ArrayList<String>(set);
  }
  
  private void traverse(int i, int j, int[][] direction, 
                        char[][] board, String testWord, Set<String> set, 
                        Map<String,Boolean> map, boolean[][] visited) {
  
      testWord += board[i][j];
  
      if (!map.containsKey(testWord)) {
          return;
      }
  
      if (map.get(testWord)) {
  
          set.add(new String(testWord));
      }
  
      for (int l = 0; l < direction.length; l++) {
  
          int adjx = i + direction[l][0];
          int adjy = j + direction[l][1];
  
          if (adjx < 0 || adjx >= board.length || adjy < 0 || adjy >= board[0].length) {
              continue;
          }
  
          if (visited[adjx][adjy]) {
              continue;
          }
  
          visited[adjx][adjy] = true;
  
          traverse(adjx, adjy, direction, board, testWord, set, map, visited);
          visited[adjx][adjy] = false;
      }
  
  }
  ```

  

  

  



