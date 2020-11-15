# 题目地址

https://www.lintcode.com/problem/lowest-common-ancestor-of-a-binary-tree



# 题目描述

```
Input：{1},1,1
Output：1
Explanation：
 For the following binary tree（only one node）:
         1
 LCA(1,1) = 1
```

**Example 2:**

```
Input：{4,3,7,#,#,5,6},3,5
Output：4
Explanation：
 For the following binary tree:

      4
     / \
    3   7
       / \
      5   6
			
 LCA(3, 5) = 4
```

### Notice

Assume two nodes are exist in tree.



# 思路

要点:

+ pq可能重复, 但一定存在于二叉树中
+ 不是BST

按照二叉树分治法模板做

**<font color = blue>返回结果是如果有p节点就返回p, 有q节点就返回q,没有就返回null</font>**



+ 首先如果root == null,那么必然没有p也没有q, return null

+ 接着如果 root == p or q就返回p或者q;

左子树返回结果 = LCA(左子树,p,q), 即左子树找到了什么,可能是LCA也可能是pq

右子树返回结果 = LCA(右子树,p,q), 即右子树找到了什么

整体:

+ 如果左子树 == null && 右子树 != null(**右边找到p或q**), <font color = blue>那么找到的就是右子树的结果, return 右子树结果</font>
+ 如果右子树 == null && 左子树 != null(**左边找到p或q**), <font color = blue>那么找到的就是左子树的结果 return 左子树结果</font>
+ 如果左右子树都 != null, <font color = blue>说明两边都找到了值, 那么root就是LCA, return root</font>
+ 最后一种可能是左右都没找到, 那自然就是return null
+ <font color = red>其实每次只要存在left或者right不是null,都可以返回root,但是已经找到pq的情况下, 这样会一直返回root而没有意思</font>

<font color = red>因为本题中pq都在树中, 所以必定存在是LCA的, 不会出现虽然左右子树有所返回但是一直没有LCA的情况</font>

# 题解

时间复杂度O(n),空间复杂度O(n)

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode A, TreeNode B) {
    // write your code here
    if (root == null) {
        return null;
    }
    if (root == A || root == B) {
        return root;
    }

    // 左右结果
    TreeNode left = lowestCommonAncestor(root.left, A, B);
    TreeNode right = lowestCommonAncestor(root.right, A, B);

    // 对整体处理
    //如果左子树 == null && 右子树 != null(右边找到p或q), 那么找到的就是右子树的结果, return右子树结果
    if (left == null && right != null) {
        return right;
    }
    //如果右子树 == null && 左子树 != null(左边找到p或q), 那么找到的就是左子树的结果, return左子树结果
    if (right == null && left != null) {
        return left;
    }
    if (left != null && right != null) {
        return root;
    }
    return null;
}
```

