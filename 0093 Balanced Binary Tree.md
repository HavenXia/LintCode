# 题目地址

https://www.lintcode.com/problem/balanced-binary-tree



# 题目描述

Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

### Example

```
Example  1:
	Input: tree = {1,2,3}
	Output: true
	
	Explanation:
	This is a balanced binary tree.
		  1  
		 / \                
		2  3

	
Example  2:
	Input: tree = {3,9,20,#,#,15,7}
	Output: true
	
	Explanation:
	This is a balanced binary tree.
		  3  
		 / \                
		9  20                
		  /  \                
		 15   7 

	
Example  3:
	Input: tree = {1,#,2,3,4}
	Output: false
	
	Explanation:
	This is not a balanced tree. 
	The height of node 1's right sub-tree is 2 but left sub-tree is 0.
		  1  
		   \                
		   2                
		  /  \                
		 3   4
```



# 思路

给定一颗二叉树, 判断是否平衡: 任意节点左右子树高度之差不超过1

本题是分治法的典型使用

<font color = red>**在做递归时,不用自己去往深了想,只要每一个点都适用,自然整体适用**</font>

isBalance: 只要确保左右子树都平衡时, 如果左右子树的高度相差小于等于1, 就直接是符合的

getHeight: 只要不是null的node, 返回左右子树的最大高度 + 1



# 题解

时间复杂度O(n),每个点都会走一遍

空间复杂度O(1)

```java
public boolean isBalanced(TreeNode root) {
    // write your code here
    if (root == null){
        return true;
    }

    if (!isBalanced(root.left)){
        return false;
    }
    if (!isBalanced(root.right)){
        return false;
    }
    return Math.abs(getHeight(root.left) - getHeight(root.right)) <= 1;

}


private int getHeight(TreeNode root){

    if (root == null){
        return 0;
    }

    return Math.max(getHeight(root.left),getHeight(root.right)) + 1; // 分治法
}
```

