# 题目地址

https://www.lintcode.com/problem/maximum-subtree



## 题目描述

Given a binary tree, find the subtree with maximum sum. Return the root of the subtree.

### Example

Example 1:

```
Input:
{1,-5,2,0,3,-4,-5}
Output:3
Explanation:
The tree is look like this:
     1
   /   \
 -5     2
 / \   /  \
0   3 -4  -5
The sum of subtree 3 (only one node) is the maximum. So we return 3.
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
It's guaranteed that there is only one subtree with maximum sum and the given binary tree is not an empty tree.



# 思路

需要给出总和最大的子树的root节点,<font color = blue>重点在于和左右子树比大小</font>

使用分治法的思想,先写出getMaxSum这个helper函数

<font color = blue>但是此处要求的是root节点,所以不得不设置一个全局变量result和maxWeight</font>

+ if root == null, return 0

+ 左子树Sum = getMaxSum(root.left)

+ 右子树Sum = getMaxSum(root.right)

+ 整棵树 = 左子树Sum + 右子树Sum + root.val

  <font color = red>if 整棵树结果 > maxWeight, maxWeight = 整棵树结果, result = root</font>

**分治法本体架构是不变的,但是在每次得到整颗树结果的时候需要和全局的max比较一下,如果出现了更大的树就把结果改为root**





# 题解

时间复杂度O(n)

空间复杂度O(n)

```java
public int maxWeight = Integer.MIN_VALUE;
public TreeNode result = null;
public TreeNode findSubtree(TreeNode root) {
    // null case
    if (root == null) {
        return root;
    }
    // 用分治法进行计算
    getMaxSum(root);
    return result;
}

private int getMaxSum(TreeNode root) {
    if (root == null) {
        return 0;
    }

    int leftSum = getMaxSum(root.left);
    int rightSum = getMaxSum(root.right);
    // 尽量少声明局部变量,所以此处不声明TotalSum
    if ((leftSum + rightSum + root.val) > maxWeight) {
        maxWeight = leftSum + rightSum + root.val;
        result = root;
    }

    return leftSum + rightSum + root.val;
}
```

