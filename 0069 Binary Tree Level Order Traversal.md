# 题目地址

https://www.lintcode.com/problem/binary-tree-level-order-traversal



# 题目描述

Given a binary tree, return the *level order* traversal of its nodes' values. (ie, from left to right, level by level).

### Example

**Example 1:**

```
Input：{1,2,3}
Output：[[1],[2,3]]
Explanation：
  1
 / \
2   3
it will be serialized {1,2,3}
level order traversal
```

**Example 2:**

```
Input：{1,#,2,3}
Output：[[1],[2],[3]]
Explanation：
1
 \
  2
 /
3
it will be serialized {1,#,2,3}
level order traversal
```

### Challenge

Challenge 1: Using only 1 queue to implement it.

Challenge 2: Use BFS algorithm to do it.

### Notice

- The first data is the root node, followed by the value of the left and right son nodes, and "#" indicates that there is no child node.
- The number of nodes does not exceed 20.





# 思路

+ 单队列

  即用一个Queue

  需要用<font color = blue>分层遍历,因为需求的是每一层一个list,而不是全部在一个list里面</font>

+ 双队列

  用queue和nextQueue互相遍历

  给第一个队列放入root,然后每次循环将队列一中节点的左右子节点放入队列二,循环最后<font color = blue>让队列1 = 队列2</font>

  每次循环第一步是把队列一的元素变成一个list加入答案中

+ dummy node

  先把root和dummynode放入queue(<font color = blue>offer(null)</font>)

  把root的左右子节点放入queue, <font color = red>遍历到null的时候意味着上一层走到dummy了, 于是也加入一个null</font>

  **但是注意,这样最后一行的右边会有两个null, 第二个null是在走到第一个null的时候加入的**

  如果遍历的时候遇到null

  + 如果levelList.size() == 0,说明走到了最后一行的最后一个null,跳出循环
  + 如果没走完,就把当前的levelList加入到大list里面,并且new一个levelList, 并且把null节点加进去,然后**continue**进入下个循环



# 题解

### 单队列

时间复杂度O(n),空间复杂度O(n)<font color = blue>Queue最多可能同时有n个元素(n为最宽那层的节点个数)</font>

但levellist这个List的空间并不算在内,因为<font color = red>它是题目所返回值的一部分,不算额外空间消耗</font>

```java
public class TreeNode {
      public int val;
      public TreeNode left, right;
      public TreeNode(int val) {
          this.val = val;
          this.left = this.right = null;
      }
}
```

```java
public List<List<Integer>> BreadthFirstSearch(TreeNode root) {
    
    List<List<Integer>> list = new ArrayList<List<Integer>>();
    List<Integer> levelList;

    if (root == null){
        return list;
    }

    Queue <TreeNode> queue = new LinkedList<TreeNode>();
    // 加入根节点
    queue.offer(root);

    while (!queue.isEmpty()){
        // 分层遍历
        // 每一层全部输出完了才进入下一层
        int n = queue.size();  
        levelList = new ArrayList<Integer>();
        for (int i=0;i<n;i++){
            TreeNode cur = queue.poll(); 
            levelList.add(cur.val);
            // 每加入一个节点的value,都要把它左右子节点都加进去
            if (cur.left != null){
                queue.offer(cur.left);
            }
            if (cur.right != null){
                queue.offer(cur.right);
            }
        }
        list.add(levelList);
    }
    return list;
}
```



### 双队列

时间复杂度O(n),空间复杂度稍微大于一点O(n)

```java
public List<List<Integer>> levelOrder(TreeNode root) {
   
    List<List<Integer>> list = new ArrayList<List<Integer>>();
    if (root == null){
        return list;
    }
	// 现在只需要一个队列了
    Queue <TreeNode> queue = new LinkedList<TreeNode>();
    queue.offer(root);

    while (!queue.isEmpty()){
		
        int n = queue.size();  
        Queue <TreeNode> nextQueue = new LinkedList<TreeNode>();
        // 每次把队列一放入答案中
        list.add(toList(queue));
		
        for (int i=0;i<n;i++){
            TreeNode cur = queue.poll(); 

            if (cur.left != null){
                nextQueue.offer(cur.left);
            }
            if (cur.right != null){
                nextQueue.offer(cur.right);
            }
        }
        // 循环最后一步是让queue指向nextQueue
        queue = nextQueue;
    }
    return list;
}
// 把队列1变为list的方法
private List<Integer> toList(Queue<TreeNode> queue) {
    List<Integer> levelList = new ArrayList<Integer>();
    for (TreeNode node: queue) {
        levelList.add(node.val);
    }
    return levelList;
}
```

### Dummy Node

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    // 声明宝宝
    List<List<Integer>> list = new ArrayList<List<Integer>>();
    List<Integer> levelList = new ArrayList<Integer>();

    if (root == null){
        return list;
    }

    Queue <TreeNode> queue = new LinkedList<TreeNode>();
    queue.offer(root);

    queue.offer(null);

    while (!queue.isEmpty()){
		
        TreeNode cur = queue.poll(); 
        if (cur == null) {
			// 这里是走到最后一层的最后一个null的时候的情况
            if (levelList.size() == 0) {
                break;
            }
			// 加入levelList
            list.add(levelList);
            levelList = new ArrayList<Integer>();
            queue.offer(null);
            continue;
        }
        levelList.add(cur.val);
        if (cur.left != null){
            queue.offer(cur.left);
        }
        if (cur.right != null){
            queue.offer(cur.right);
        }
    }
    return list;
}
```



