# 题目地址

https://www.lintcode.com/problem/binary-search-tree-iterator



# 题目描述

Design an iterator over a binary search tree with the following rules:

- Elements are visited in ascending order (i.e. an in-order traversal)
- `next()` and `hasNext()` queries run in O(*1*) time in **average**.

### Example

**Example 1**

```
Input:  {10,1,11,#,6,#,12}
Output:  [1, 6, 10, 11, 12]
Explanation:
The BST is look like this:
  10
  /\
 1 11
  \  \
   6  12
You can return the inorder traversal of a BST [1, 6, 10, 11, 12]
```

**Example 2**

```plain
Input: {2,1,3}
Output: [1,2,3]
Explanation:
The BST is look like this:
  2
 / \
1   3
You can return the inorder traversal of a BST tree [1,2,3]
```

### Challenge

Extra memory usage O(h), h is the height of the tree.

**Super Star**: Extra memory usage O(1)



# 思路

详见[BSTIterator](/Users/parallax/Documents/CS/九章算法课/07 递归,DFS,回溯.md)

最经典的非递归实现二叉树遍历



# 题解

时间复杂度O(n), 空间复杂度O(h)

```java
public class BSTIterator {
    
    public Stack<TreeNode> stack;

    public BSTIterator(TreeNode root) {
        // do intialization if necessary
        stack = new Stack<TreeNode>();
        findMostLeft(root);
    }

    private void findMostLeft(TreeNode node) {
        while (node != null) {
            stack.add(node);
            node = node.left;
        }
    }

    public boolean hasNext() {
        // write your code here
        return !stack.isEmpty();
    }

    public TreeNode next() {
        // write your code here
        TreeNode node = stack.pop();
        if (node.right != null) {
            findMostLeft(node.right);
        }
        return node;
    }
}
```

