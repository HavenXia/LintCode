# 题目地址

https://www.lintcode.com/problem/topological-sorting



# 题目描述

Given an directed graph, a topological order of the graph nodes is defined as follow:

- For each directed edge `A -> B` in graph, A must before B in the order list.
- The first node in the order can be any node in the graph with no nodes direct to it.

Find any topological order for the given graph.

### Example

For graph as follow:

![图片](https://media-cdn.jiuzhang.com/markdown/images/8/6/91cf07d2-b7ea-11e9-bb77-0242ac110002.jpg "align = left")

The topological order can be:

```
[0, 1, 2, 3, 4, 5]
[0, 2, 3, 1, 5, 4]
...
```

### Challenge

Can you do it in both BFS and DFS?

### Notice

You can assume that there is at least one topological order in the graph.



# 思路

拓扑排序的基本题目

+ 首先统计所有入度, 用hashmap可以完成,**每次给neighbor增加数其实就是入度**

  ```java
  for (Node node : graph) {
      for (Node neighbor : node.neighbors) {
          if (map.containsKey(neighbor)) {
              map.put(neighbor, map.get(neighbor) + 1);
          } else {
              map.put(neighbor, 1);
          }
      }
  }
  ```

+ 声明一个queue, 把入度为0(<font color = blue>map中不包含的node</font>) 放入queue 和 result(return 所用的arrayList)中

+ 按BFS模板,用while套着,每次从队列中取出一个<font color = red>入度为0</font>的node, 对该node的neighbors遍历, **将它们的map.get(neighbor) - 1**, 其实就是去掉了从当前node到该neighbor的入度
+ 并且如果此时该neighbor的入度变为0, 就将它加入result和queue,最后返回queue即可



# 题解

时间复杂度: O(n + m), <font color = blue>总的来看计算入度是每条边计算一次, 而queue是每个点poll一次</font>

空间复杂度:O(n) 因为存放的都是节点

```java
public ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
        // write your code here
        ArrayList<DirectedGraphNode> result = new ArrayList<DirectedGraphNode>();
        Map<DirectedGraphNode, Integer> map = new HashMap<DirectedGraphNode, Integer>();
        // 统计入度为0的node
        for (DirectedGraphNode node : graph) {
            for (DirectedGraphNode neighbor : node.neighbors) {
                if (map.containsKey(neighbor)) {
                    map.put(neighbor, map.get(neighbor) + 1);
                } else {
                    map.put(neighbor, 1);
                }
                
            }
        }
        // 放入queue
        Queue<DirectedGraphNode> queue = new LinkedList<DirectedGraphNode>();
        for (DirectedGraphNode node : graph) {
            if (!map.containsKey(node)) {
                queue.offer(node);
                result.add(node);
            }
        }
        // 遍历消入度
        while (!queue.isEmpty()) {
            DirectedGraphNode node = queue.poll();
            for (DirectedGraphNode neighbor : node.neighbors) {
                map.put(neighbor, map.get(neighbor) - 1);
                if (map.get(neighbor) == 0) {
                    result.add(neighbor);
                    queue.offer(neighbor);
                } 
            }
        }
        return result;
    }
```

