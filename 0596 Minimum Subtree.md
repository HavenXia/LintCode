# 题目地址

https://www.lintcode.com/problem/minimum-subtree



# 题目描述

Given a binary tree, find the subtree with minimum sum. Return the root of the subtree.

### Example

Example 1:

```
Input:
{1,-5,2,1,2,-4,-5}
Output:1
Explanation:
The tree is look like this:
     1
   /   \
 -5     2
 / \   /  \
1   2 -4  -5 
The sum of whole tree is minimum, so return the root.
```

Example 2:

```
Input:
{1}
Output:1
Explanation:
The tree is look like this:
   1
There is one and only one subtree in the tree. So we return 1.
```

### Notice

LintCode will print the subtree which root is your return node.
It's guaranteed that there is only one subtree with minimum sum and the given binary tree is not an empty tree.



# 思路

一棵二叉树有O(n)棵子树

按二叉树分治法模板做

### 使用全局变量的分治法

整棵树的返回结果 = 左子树结果 + 右子树结果 + 根节点val

接着检查一下是否当前的sum更小, 是的话就把result node替换成当前node

<font color = blue>左右子树的sum可以不声明, 节省空间</font>

时间复杂度O(n), 空间复杂度O(n),<font color = blue>二叉树最坏情况下要递归n次</font>

<font color = red>但是本题用了class内的全局变量, 其实并不够好</font> 

### 纯分治法

可以用一个ResultType class, 包括 `Treenode minSubtree` , `int minSum` , `int sum` 

左右子树的结果写出来

**<font color = red>注意: root = null的时候, minSum要设置成Integer.MAX_VALUE, 确保null不会成为minSubtree, 但是sum得是0, 以供计算</font>**

**这样如果一个节点没有左右子节点,必定是返回他自己**

先把整棵树的的minSum和sum都写成sum, minSubtree写成当前node

然后分别检查 <font color = blue>是否左右子树的sum更小, 是的话就把整棵树的minSum和minSubtree都替换一下</font>

代码量更大但是更安全

时间复杂度O(n), 空间复杂度O(n),<font color = blue>二叉树最坏情况下要递归n次</font>



# 题解

### 使用全局变量的分治法

时间复杂度O(n), 空间复杂度O(1)

```java
public class Solution {
    int minSum;
    TreeNode minRoot;

    public TreeNode findSubtree(TreeNode root) {
        // write your code here
        minRoot = root;
        minSum = Integer.MAX_VALUE;
        getMinSum(root);
        return minRoot;
    }

    private int getMinSum(TreeNode root) {
        if (root == null) {
            return 0;
        }
        // 省略左右sum的声明, 减少stack占用
        int sum = getMinSum(root.left) + getMinSum(root.right) + root.val;
        if (sum < minSum) {
            minSum = sum;
            minRoot = root;
        }
        return sum;
    }
}
```

### 纯分治法

时间复杂度O(n), 空间复杂度O(1)

```java
class ResultType {
    public TreeNode minSubtree;
    public int sum, minSum;
    public ResultType(TreeNode minSubtree, int minSum, int sum) {
        this.minSubtree = minSubtree;
        this.minSum = minSum;
        this.sum = sum;
    }
}
```

```java
public TreeNode findSubtree(TreeNode root) {
    ResultType result = helper(root);
    return result.minSubtree;
}

public ResultType helper(TreeNode node) {
    // null case 时minSum用最大值,确保肯定大于任何sum
    // 但是sum得用0, 因为会被用到计算其他sum
    if (node == null) {
        return new ResultType(null, Integer.MAX_VALUE, 0);
    }

    ResultType leftResult = helper(node.left);
    ResultType rightResult = helper(node.right);

    ResultType result = new ResultType(
        node,
        leftResult.sum + rightResult.sum + node.val,
        leftResult.sum + rightResult.sum + node.val
    );

    if (leftResult.minSum <= result.minSum) {
        result.minSum = leftResult.minSum;
        result.minSubtree = leftResult.minSubtree;
    }

    if (rightResult.minSum <= result.minSum) {
        result.minSum = rightResult.minSum;
        result.minSubtree = rightResult.minSubtree;
    }

    return result;
}
```

()