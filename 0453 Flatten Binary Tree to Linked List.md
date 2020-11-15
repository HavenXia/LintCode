# 题目地址

https://www.lintcode.com/problem/flatten-binary-tree-to-linked-list



# 题目描述

Flatten a binary tree to a fake "linked list" in pre-order traversal.

Here we use the *right* pointer in TreeNode as the *next* pointer in ListNode.

### Example

**Example 1:**

```
Input:{1,2,5,3,4,#,6}
Output：{1,#,2,#,3,#,4,#,5,#,6}
Explanation：
     1
    / \
   2   5
  / \   \
 3   4   6

   1
    \
     2
      \
       3
        \
         4
          \
           5
            \
             6
```

**Example 2:**

```
Input:{1}
Output:{1}
Explanation：
         1
         1
```

### Challenge

Do it in-place without any extra memory.

### Notice

Don't forget to mark the left child of each node to null. Or you will get Time Limit Exceeded or Memory Limit Exceeded.



# 思路

需要做in-place的改变, 要求按照<font color = blue>前序遍历</font>的顺序, 将其排列成类似链表的结构

### 使用递归的遍历法

需要定义一个全局变量lastNode, 代表前序遍历走过的上一个节点

每次执行的时候, 检测一下lastNode是不是null, 如果不是null, <font color = blue>就将lastnode的左节点设为0, 右节点为当前节点</font>

此处的其奥妙在于作为全局节点, lastNode不会回到当前循环中的值, <font color = red>而是随着前序遍历一直变动</font>

需要注意的是, 在执行完 flatten(root.left)后, root的right已经被改变了(<font color = blue>因为root作为lastNode进入了node.left的执行, 它的右子变成了左子, 所以要用一个right 来指向root.right</font>)

但是因为这个right不是全局变量, 是和当前的root一直同步的

时间复杂度O(n), 空间复杂度O(n)

### 纯分治法

是一个后序遍历

左子树返回结果应该为(左子树最终节点),右子树返回结果应该为(右子树最终节点)

然后让<font color = blue>根节点连接左子树 firstNode, 然后左子树lastNode连接右子树firstNode</font>

**如何知道左子树的lastNode?**

改变返回值, 左右子树返回其lastNode, <font color = red>firstNode可以通过当前root.left和right来了解</font>

但最后返回的时候需要分类讨论, 左右子树都可能是null, 而需要返回的是最后一个节点



# 题解

### 使用递归的遍历法

```java
public void flatten(TreeNode root) {
    // null case 
    if (root == null) {
        return;
    }
    if (lastNode != null) {
        lastNode.left = null;
        lastNode.right = root;
    }
    // 更新lastNode
    lastNode = root;
    // 给right一个赋值
    TreeNode right = root.right;

    flatten(root.left);
    flatten(right);
}
```

### 纯分治法

```java
public void flatten(TreeNode root) {
    // null case 
    if (root == null) {
        return;
    }
    helper(root);
}
private TreeNode helper(TreeNode root) {
    // null case 
    if (root == null) {
        return root;
    }
    // 左右子树返回结果
    TreeNode leftLast = helper(root.left);
    TreeNode rightLast = helper(root.right);

    // 整棵树动作, 连接左右子树
    // 如果左子树不是null,就要连接到root和右子树之间
    // 但如果右子树是null, 并不影响操作
    if (leftLast != null) {
        // 连接顺序不能搞错!
        leftLast.right = root.right;
        root.right = root.left;
        root.left = null;
    }
    // 返回的是最后的节点, 所以要分情况考虑
    if (rightLast != null) {
        return rightLast;
    } else if (leftLast != null) {
        return leftLast;
    } else {
        return root;
    }
}
```

