# 题目地址

https://www.lintcode.com/problem/lowest-common-ancestor-iii



# 题目描述

Given the root and two nodes in a Binary Tree. Find the lowest common ancestor(LCA) of the two nodes.
The lowest common ancestor is the node with largest depth which is the ancestor of both nodes.
Return `null` if LCA does not exist.

### Example

**Example1**

```
Input: 
{4, 3, 7, #, #, 5, 6}
3 5
5 6
6 7 
5 8
Output: 
4
7
7
null
Explanation:
  4
 / \
3   7
   / \
  5   6

LCA(3, 5) = 4
LCA(5, 6) = 7
LCA(6, 7) = 7
LCA(5, 8) = null
```

**Example2**

```
Input:
{1}
1 1
Output: 
1
Explanation:
The tree is just a node, whose value is 1.
```

### Notice

node A or node B may not exist in tree.
Each node has a different value



# 思路

本题是对[Lowest Common Ancestor of a Binary Tree](/Users/parallax/Documents/CS/LintCode/0088 Lowest Common Ancestor of a Binary Tree.md)的升级版

<font color = blue>pq可能相等, 且不一定都在二叉树中,因此有可能出现LCA结果是null</font>

因此本题的return type不能只有TreeNode了, 这样的话并不能分清是否存在LCA

因此需要在外面写一个helper(TreeNode A, TreeNode B, TreeNode node)函数

所以需要另外两个参数, a_exist 和b_exist, <font color = red>只有这两个参数同时为true时, TreeNode的返回才是LCA</font>



首先如果root == null, return (false, false, null)



+ 左子树返回结果left = helper(A, B, root.left)
+ 右子树返回结果right = helper(A, B, root.right)

**ReturnType(boolean a_exist, boolean b_exist, TreeNode root)**

<font color = red>为了避免code reuse, 增加 a_exist = left.a_exist || right.a_exist || root == A</font> 

​							      	    	<font color = red>b_exist = left.b_exist || right.b_exist || root == B</font> 		

接着

+ 如果root == p或者q, <font color = blue>说明p或者q存在了, 当前找到东西了,return (a_exist, b_exist, root)</font>

+ 如果left.node != null && right.node != null, <font color = blue>说明左右两边各找到了一个点,那么LCA就是root, return(a_exist, b_exist, root)</font>
+ 如果left.node == null && right.node != null,<font color = blue>右边找到东西了,返回右边结果,return(a_exist, b_exist, right.node)</font> 
+ 如果left.node != null && right.node == null,<font color = blue>左边找到东西了,返回做边结果,return(a_exist, b_exist, left.node)</font> 

+ 最后,如果left和right都是null, 那说明两个点都不在,<font color = blue>return(false, false, null)</font>



注意:在主函数中求得整个树的返回值时, 需要检测

<font color = red>是否其a_exist 和b_exist都为true(确保都存在), 是的话就return node, 不是就return null</font>



# 题解

时间复杂度O(n), 空间复杂度O(n)

首先是ReturnType class

```java
class ResultType {
    public boolean a_exist, b_exist;
    public TreeNode node;
    ResultType(boolean a_exist, boolean b_exist, TreeNode node) {
        this.a_exist = a_exist;
        this.b_exist = b_exist;
        this.node = node;
    }
}
```

```java
public class Solution {
    public TreeNode lowestCommonAncestor3(TreeNode root, TreeNode A, TreeNode B) {
        
        ResultType wholeResult = helper(root, A, B);
        // 在主函数中求得整个树的返回值时, 需要检测是否其a_exist 和b_exist都为true(确保都存在)
        if (wholeResult.a_exist && wholeResult.b_exist)
            return wholeResult.node;
        else
            return null;
    }
    
    public ResultType helper(TreeNode root, TreeNode A, TreeNode B) {
		// 首先如果root == null, return (false, false, null)
        if (root == null)
            return new ResultType(false, false, null);
        // 左右子树返回结果
        ResultType left = helper(root.left, A, B);
        ResultType right = helper(root.right, A, B);
        // 为了避免code reuse
        boolean a_exist = left.a_exist || right.a_exist || root == A;
        boolean b_exist = left.b_exist || right.b_exist || root == B;
        // 说明p或者q存在了, 当前找到东西了,return (a_exist, b_exist, root)
        if (root == A || root == B) {
            return new ResultType(a_exist, b_exist, root);
        }
		// 说明左右两边各找到了一个点,那么LCA就是root
        if (left.node != null && right.node != null) {
            return new ResultType(a_exist, b_exist, root);
        }
        // 左边找到东西了,返回左边结果
        if (left.node != null && right.node == null) {
            return new ResultType(a_exist, b_exist, left.node);  
        }
        // 右边找到东西了,返回右边结果
        if (right.node != null && left.node == null) {
            return new ResultType(a_exist, b_exist, right.node);
        }
        // 两个点都不存在
        return new ResultType(a_exist, b_exist, null);
    }
}
```

