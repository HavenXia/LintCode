# 题目地址

https://www.lintcode.com/problem/maximum-depth-of-binary-tree



# 题目描述

Given a binary tree, find its maximum depth.

The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

### Example

**Example 1:**

```
Input: tree = {}
Output: 0
Explanation: The height of empty tree is 0.
```

**Example 2:**

```
Input: tree = {1,2,3,#,#,4,5}
Output: 3	
Explanation: Like this:
   1
  / \                
 2   3                
    / \                
   4   5
it will be serialized {1,2,3,#,#,4,5}
```

### Notice

The answer will not exceed `5000`





# 思路

遍历法:

设一个全局变量height, 只要在遍历过程中有depth大于height的,就让height == depth

这样遍历所有节点,即可得到最大深度height

**分治法:**

左子树高度=maxDepth(root.left), 右子树高度= maxDepth(root.right)

<font color = blue>整棵树高度 = max(左右子树高度) + 1</font>

null case: 高度为0

<font color = red>更加易懂简洁</font>



# 题解

### 遍历法

时间复杂度O(n),空间复杂度O(1)

```java
private 
public int maxDepth(TreeNode root) {
    
    // write your code here
    traverse(root,1);
    return height;

}
private void traverse(TreeNode root,int depth){
    if (root == null){
        return;
    }
    height = Math.max(depth,height);
    traverse(root.left,depth + 1);
    traverse(root.right,depth + 1);
}
```



### 分治法

时间复杂度O(n),空间复杂度O(1)

```java
public class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }

        int left = maxDepth(root.left);
        int right = maxDepth(root.right);
        return Math.max(left, right) + 1;
    }
}
```

