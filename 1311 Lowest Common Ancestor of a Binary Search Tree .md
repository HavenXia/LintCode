# 题目地址

https://www.lintcode.com/problem/lowest-common-ancestor-of-a-binary-search-tree



# 题目描述

Given a binary search tree (BST), find the lowest common ancestor (LCA) of two given nodes in the BST.

According to the [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow **a node to be a descendant of itself**).”

Given binary search tree: root = {6,2,8,0,4,7,9,#,#,3,5}

![图片](https://media-cdn.jiuzhang.com/markdown/images/7/6/660f4a1a-9ff0-11e9-a529-0242ac110002.jpg)

### Example

**Example 1:**

```
Input: 
{6,2,8,0,4,7,9,#,#,3,5}
2
8
Output: 6
Explanation: The LCA of nodes 2 and 8 is 6.
```

**Example 2:**

```
Input: 
{6,2,8,0,4,7,9,#,#,3,5}
2
4
Output: 2
Explanation: The LCA of nodes 2 and 4 is 2, since a node can be a descendant of itself according to the LCA definition.
```

### Notice

- **All of the nodes' values will be unique.**
- **p and q are different and both values will exist in the BST.**



# 思路

首先这题的不同在于这是<font color = blue>BST, 且给定了每个点的值都不一样,**且pq不是同一个点**</font>

<font color = blue>要注意到BST左子树所有的值都小于它, 右子树所有的值都大于它</font>

还是使用分治法的思想,但是本题不同于模板

左右子树的结果不需要参与到整棵树结果中,而是左右子树只要择其一进入(<font color = red>剪枝</font>)

首先需要明确的是, **确保pq一定需要在root的子树中**

+ 如果 root 的值大于pq的值, <font color = blue>说明pq都在root的左子树</font>, 继续求<font color = red>LCA(root.left)</font>
+ 如果 root 的值小于pq的值, <font color = blue>说明pq都在root的右子树</font>, 继续求<font color = red>LCA(root.right)</font>

+ 如果root的值在pq中间, 说明root就是pq的LCA, 注意: <font color = red>因为上面两句可以确保pq都是root的子树中的</font>

+ 如果root==p或q, 说明root就是pq的LCA,注意:<font color = red>因为上面两句可以确保pq都是root的子树中的</font>

  <font color = blue>这里为什么不用val比较? 因为考虑到</font>





# 题解

时间复杂度O(N), 空间复杂度O(N), <font color = blue>来自递归深度最大可能是n</font>

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    // 首先确保pq都是在root的子树
    // 如果pq和root相等,那必然就是另一个点是子树中的节点
    if (root.val == p.val || root.val == q.val) {
        return root;
    }
    if (root.val > p.val && root.val > q.val) {
        return lowestCommonAncestor(root.left, p, q);
    }
    if (root.val < p.val && root.val < q.val) {
        return lowestCommonAncestor(root.right, p, q);
    }
    // p, q 分别在左右子树，那么root即为结果
    return root;
}
```

