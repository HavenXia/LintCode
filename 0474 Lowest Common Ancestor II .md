# 题目地址

https://www.lintcode.com/problem/lowest-common-ancestor-ii/



# 题目描述

Given the root and two nodes in a Binary Tree. Find the lowest common ancestor(LCA) of the two nodes.

The lowest common ancestor is the node with largest depth which is the ancestor of both nodes.

The node has an extra attribute `parent` which point to the father of itself. The root's parent is null.

### Example

**Example 1:**

```
Input：{4,3,7,#,#,5,6},3,5
Output：4
Explanation：
     4
     / \
    3   7
       / \
      5   6
LCA(3, 5) = 4
```

**Example 2:**

```
Input：{4,3,7,#,#,5,6},5,6
Output：7
Explanation：
      4
     / \
    3   7
       / \
      5   6
LCA(5, 6) = 7
```





# 思路

求最近的公共父节点

每一个节点有一个<font color = blue>attribute: parent</font> ,父节点

+ 使用 HashSet 记录从 A 到根的所有点

  <font color = blue>用一个while(node != null), 就将node加入Hashset, 然后node = node.parent</font> 

  先加入node再变为parent是为了保证等循环到根节点时候, 加入根节点就停止了,<font color = green>不会加入根节点的parent节点</font>

+ 循环访问从B到根的所有点, <font color = red>但是在每次循环中都检测一下是否当前的这个点已经在A的hashSet里了,如果是直接返回就好 </font>

时间复杂度为O(n), <font color = blue>因为不一定是平衡树</font>

空间复杂度O(n), 可能全部装进去了

### 如果OV不让用HashSet怎么办?

<font color = red>选用Stack来做</font>

首先按照遍历把A到root, B到root的节点存在stack里

然后对两个stack分别pop, 首先第一个点肯定都是根节点, 然后继续看下一个位置

如果<font color = blue>某个位置两个stack pop出来的不一样了, 那就说明上一个pop出来的就是最近的公共父节点</font>

<font color = red>而如果出循环一直没有return, 说明两条路径是重合的, 那就直接return node即可</font> 

非常的巧妙

时间复杂度为O(n),空间复杂度O(n)



# 题解

时间复杂度为O(n),空间复杂度O(n)

```java
public ParentTreeNode lowestCommonAncestorII(ParentTreeNode root, ParentTreeNode A, ParentTreeNode B) {
    // 声明HashSet
    Set<ParentTreeNode> parents = new HashSet<ParentTreeNode>();

    // 存储所有A的父节点
    ParentTreeNode node = A;
    while (node != null) {
        parents.add(node);
        node = node.parent;
    }
    // 从b开始往上访问,检测是否出现相同节点
    node = B;
    while (node != null) {
        if (parents.contains(node)) {
            return node;
        }
        node = node.parent;
    }
    // 出循环意味着没有公共父节点
    return null;
}
```



### 不用HashSet的做法

```java
public ParentTreeNode lowestCommonAncestorII(ParentTreeNode root, ParentTreeNode A, ParentTreeNode B) {
    // write your code here
    Stack<ParentTreeNode> parentsA = new Stack<ParentTreeNode>();
    Stack<ParentTreeNode> parentsB = new Stack<ParentTreeNode>();

    // 存储所有A的父节点
    ParentTreeNode node = A;
    while (node != null) {
        parentsA.push(node);
        node = node.parent;
    }
    // 存储所有B的父节点
    node = B;
    while (node != null) {
        parentsB.push(node);
        node = node.parent;
    }
    // 按循环stack找最近父节点
    int n = parentsA.size();
    int m = parentsB.size();
    node = root;
    // 此处必须用nm,防止每次循环的条件变化
    for (int i = 0; i < Math.min(n, m); i++) {
        if (parentsA.peek() != parentsB.peek()) {
            return node;
        }
        node = parentsB.pop();
        parentsA.pop();
    }
    // 出循环只有可能是其中一个就是另一个点父节点,那么node正好就是它的值
    return node;
}
```

