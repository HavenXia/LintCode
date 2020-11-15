# 题目地址

https://www.lintcode.com/problem/clone-graph



# 题目描述

Clone an undirected graph. Each node in the graph contains a `label` and a list of its `neighbors`. Nodes are labeled uniquely.

You need to return a deep copied graph, which has the same structure as the original graph, and any changes to the new graph will not have any effect on the original graph.

### Example

**Example1**

```
Input:
{1,2,4#2,1,4#4,1,2}
Output: 
{1,2,4#2,1,4#4,1,2}
Explanation:
1------2  
 \     |  
  \    |  
   \   |  
    \  |  
      4   
```

### Clarification

How we serialize an undirected graph: http://www.lintcode.com/help/graph/

### Notice

You need return the node with the same label as the input node.



# 思路

**默认是conntected graph**

图里面每个node有两个属性,分别是label和a list of neighbors

按照BFS的思想来做

首先创建一个queue存入node, 并且创建一个HashMap<Node, Node>并存入旧node和新node

从queue中poll一个node, 检测它的所有<font color = blue>neighbor nodes</font>

+ <font color = blue>如果某个node在HashMap中还没存在, 就put进key = 旧node, value = 新node,</font><font color = red>并且把这个node放入queue中</font>

+ 并且把所有的map.get(neighbor nodes) 都添加到当前map.get(node)的neighbors属性里去

  + <font color = red>这一步必须后做,因为需要把所有的node都先加到map里才行!确保所有的node都已经存在于map了</font>

  + <font color = red>对于当前的map.get(cur node), 它的neighbors属性必须也得加入**新创建的node,绝对不能用旧node!!**</font>

本题中不需要分层, 代码可读性更高, 因为本题不要求每一层的信息被分开

### 复杂度

### <font color = red>时间复杂度与最内层循环主体的执行次数有关,与有多少重循环无关</font>

内层循环主体最多执行了2m次(<font color = blue>m是边的总数</font>) , 外层循环主体最多执行n次(<font color = blue>n是点的个数</font>)

**所以正确的时间复杂度应该是O(2m) + O(n) == O(m) + O(n) == O(max(m,n)) == O(n+m)**





# 题解

时间复杂度O(n+m),空间复杂度O(1)

首先是节点定义

```java
class UndirectedGraphNode {
    int label;
    List<UndirectedGraphNode> neighbors;
    UndirectedGraphNode(int x) {
        label = x;
        neighbors = new ArrayList<UndirectedGraphNode>();
    }
}
```

```java
public UndirectedGraphNode cloneGraph(UndirectedGraphNode node) {
    // null case
    if (node == null) {
        return node;
    }
	// 创建一个queue和一个HashMap
    Queue<UndirectedGraphNode> queue = new LinkedList<>();
    Map<UndirectedGraphNode, UndirectedGraphNode> map = new HashMap<>();
    // 把最初的node分别放入queue和map
    queue.offer(node);
    map.put(node, new UndirectedGraphNode(node.label));
	// 当所有的都被poll出来,所有节点都走过了
    // 更重要的一点是: 必须是conntect graph, 否则没法走完
    while (!queue.isEmpty()) {
		// 此处执行次数最多是O(n)
        UndirectedGraphNode cur = queue.poll();
		
        for (int i = 0; i < cur.neighbors.size(); i++) {
            // 此处最多执行次数是O(2m)
            if (!map.containsKey(cur.neighbors.get(i))) {
                map.put(cur.neighbors.get(i), new UndirectedGraphNode(cur.neighbors.get(i).label));
                queue.offer(cur.neighbors.get(i));
            }
            // 此处一定要放入新生成的node, 而不是旧node, 很大的陷阱
            map.get(cur).neighbors.add(map.get(cur.neighbors.get(i)));
        }
    }

    return map.get(node);
}
```

