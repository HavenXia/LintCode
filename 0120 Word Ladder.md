# 题目地址

https://www.lintcode.com/problem/word-ladder



# 题目描述

iven two words (*start* and *end*), and a dictionary, find the shortest transformation sequence from *start* to *end*, output the length of the sequence.
Transformation rule such that:

1. Only one letter can be changed at a time
2. Each intermediate word must exist in the dictionary. (Start and end words do not need to appear in the dictionary )

### Example

**Example 1:**

```
Input：start = "a"，end = "c"，dict =["a","b","c"]
Output：2
Explanation：
"a"->"c"
```

**Example 2:**

```
Input：start ="hit"，end = "cog"，dict =["hot","dot","dog","lot","log"]
Output：5
Explanation：
"hit"->"hot"->"dot"->"dog"->"cog"
```

### Notice

- Return 0 if there is no such transformation sequence.
- All words have the same length.
- All words contain only lowercase alphabetic characters.
- You may assume no duplicates in the word list.
- You may assume beginWord and endWord are non-empty and are not the same.



# 思路

需要给出的是sequence的长度,每次只能change一个letter, 并且每一个中间词都必须在给定的array中, 比如

+ "a"——"c" 说明sequence的长度是2
+ "hit"——"hot"——"dot"——"dog"——"cog"的长度是5

<font color = blue>没有给定图或者边一类的信息, 需要自行转化</font>

为了完成图的定义,本题真正重要的是**需要找到每个点的邻居节点**

**怎么降低这个过程的时间复杂度?**

+ 直接对每一个单词进行遍历, <font color = red>时间太久了无法使用</font>
+ 每个单词的每个字母一共有25种变法,因此实际上每个单词的邻居**最多也就Length x 25 种**
+ <font color = red>一定要跳过自己, 否则检测dict的时候会检测到存在然后无限循环的</font> 

总体还是按照模板来:

+ 首先需要把end加入dict, 因为本质是在dict里查找, 所以end必须在里面

+ 把初始node放入HashSet和queue中,先套一个while, **然后length + 1**(<font color = red>因为每个当前node必然是让legnth + 1</font>)

+ 对于当前node在dict中符合标准的neighbors nodes进行遍历(<font color = red>**此处必须分层,因为一个单词可能有多种变法**</font>)
+ 如果neighbor已经存在于hashSet就走向下一个, 如果没存在就加入hashSet和queue

+ <font color = blue>如果neighbor就是end节点那就是找到了,直接返回length</font>

设dict长度为n, word长度为m



**<font color = red>为什么HashSet的contains方法对于string builder创造的string也能检测?</font>**

<font color = blue>因为HashSet和HashMap的contains方法本质上是用equality比较, 所以不同的string也可以</font>

# 题解

### 优化前

时间复杂度看似是O(26mn<sup>2</sup>), n<sup>2</sup>来源于getNeighbors函数已经用掉了O(26m), 再对他进行遍历最差需要n,再乘上外层的n

<font color = red>但是实际上时间复杂度是O(26mn), 因为getNeibors函数生成了一个迭代器,然后是在迭代器里进遍历,实际上只运行一次?并不会再次遍历</font>

```java
public int ladderLength(String start, String end, Set<String> dict) {
    // null case
    if (dict == null || dict.isEmpty()) {
        return -1;
    }
	// 如果给定的start和end相同,那就是从自己到自己,自然只有一步
    if (start.equals(end)) {
        return 1;
    }
	// 必须把end加进去
    dict.add(end);
	// 模板操作
    Set<String> set = new HashSet<String>();
    Queue<String> queue = new LinkedList<String>();
    queue.offer(start);
    set.add(start);
    
    // length初始为1
    // 但凡dict有一个值,length就是2了
    int length = 1;

    while (!queue.isEmpty()) {
        length++;
	
        // 必须分层,保证每层结束length才能++, 比如"hit"到"cog"
        // ["hot","dot","dog","lot","log"]
        // 首先hit到hot(length = 2),然后hot到dot和lot(length = 3)
        // 如果不分层的话, dot到dog(length = 4), lot到log(length = 5), 最后dog到cog(length = 6)
        // log也到cog(dog之后就return了)
        // 但如果分层, 得到就是正确答案5
        int size = queue.size();
        for (int i = 0; i < size; i++) {

            String word = queue.poll();
            for (String neighbor : getNeighbors(word, dict)) {
				// 如果已经存在了就直接跳过
                if (set.contains(neighbor)) {
                    continue;
                }
				// 这里是唯一的真正出口,能够变为end了,直接返回length
                if (neighbor.equals(end)) {
                    return length;
                }
				// 如果没有遇到过就加入set和queue
                set.add(neighbor);
                queue.offer(neighbor);
            }
        }
    }
    return -1;
}

// 返回当前word在改变一个letter情况下且出现在dict中的所有变体的List
private List<String> getNeighbors(String word, Set<String> dict) {
    List<String> neighbors = new ArrayList<String>();
	// 每个字母从'a'到'z',char是可以直接++的
    for (char letter = 'a'; letter <= 'z'; letter++) {
        // 每个单词从第一个字母到最后一个
        for (int i = 0; i < word.length(); i++) {
			// 如果正好是原位的字母,就跳过
            if (letter == word.charAt(i)) {
                continue;
            }
            // 否则就得到当前的替换值, 如果它在dict里,就放入List中
            String neighbor = replace(word, i, letter);
            if (dict.contains(neighbor)) {
                neighbors.add(neighbor);
            }
        }
    }
    return neighbors;
}

// 把某个word的某个index换成给定的letter并返回
private String replace(String word, int index, char letter) {
    char[] letters = word.toCharArray();
    letters[index] = letter;
    // 直接用new String形成string
    return new String(letters);
}
```



### 优化后

时间复杂度为O(26mn), 此时26mn就是最坏情况下的总边数

空间复杂度O(n), <font color = red>因为有set.contains的存在, 确保了最多只有n个word在queue里</font>

```java
public int ladderLength(String start, String end, Set<String> dict) {
    // null case
    if (dict == null || dict.isEmpty()) {
        return -1;
    }
	// 如果给定的start和end相同,那就是从自己到自己,自然只有一步
    if (start.equals(end)) {
        return 1;
    }
	// 必须把end加进去
    dict.add(end);
	// 模板操作
    Set<String> set = new HashSet<String>();
    Queue<String> queue = new LinkedList<String>();
    queue.offer(start);
    set.add(start);
    
    // length初始为1
    // 但凡dict有一个值,length就是2了
    int length = 1;

    while (!queue.isEmpty()) {
        length++;

        // 必须分层,保证每层结束length才能++, 比如"hit"到"cog"
        // ["hot","dot","dog","lot","log"]
        // 首先hit到hot(length = 2),然后hot到dot和lot(length = 3)
        // 如果不分层的话, dot到dog(length = 4), lot到log(length = 5), 最后dog到cog(length = 6)
        // log也到cog(dog之后就return了)
        // 但如果分层, 得到就是正确答案5
        int size = queue.size();
        for (int i = 0; i < size; i++) {
			
            String word = queue.poll();
            // 直接把getNeighbors整合到code里,降低时间复杂度
            for (char letter = 'a'; letter <= 'z'; letter++) {
                for (int j = 0; j < word.length(); j++) {
                    // 如果正好是原位的字母,就跳过
                    if (letter == word.charAt(j)) {
                        continue;
                    } 
                    // 给出替换后的next word
                    String next = replace(word, j, letter);
					// 首先必须得在dict里
                    if (dict.contains(next)) {
                        // 其次如果就是end, 就可以返回length了
                        if (next.equals(end)) {
                            return length;
                        }
                        // 如果已经出现在set里了,自然没必要在放进去了,要求的是最短路径
                        if (set.contains(next)) {
                            continue;
                        }
                        set.add(next);
                        queue.offer(next);
                    }
                }
            }
        }
    }
    return -1;
}


private String replace(String word, int index, char letter) {
    char[] letters = word.toCharArray();
    letters[index] = letter;
    return new String(letters);
}
```

