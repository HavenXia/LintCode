# 题目地址

https://www.lintcode.com/problem/kth-smallest-element-in-a-bst



# 题目描述

Given a binary search tree, write a function `kthSmallest` to find the kth smallest element in it.

### Example

**Example 1:**

```
Input：{1,#,2},2
Output：2
Explanation：
	1
	 \
	  2
The second smallest element is 2.
```

**Example 2:**

```
Input：{2,1,3},1
Output：1
Explanation：
  2
 / \
1   3
The first smallest element is 1.
```

### Challenge

What if the BST is modified (insert/delete operations) often and you need to find the kth smallest frequently? How would you optimize the kthSmallest routine?

### Notice

You may assume k is always valid, `1 ≤ k ≤ BST's total elements`.



# 思路

其实问的就是BST中从小到大第K个点

按照[改进后的BST遍历模板](/Users/parallax/Documents/求职/九章算法课/07 递归,DFS,回溯.md), 返回的第K个值就是从小到大第K个点

首先把最左边一列加入stack,然后弹出第一个节点作为node

<font color = blue>如果node.right != null, 说明右子树存在, 加入其右子节点和它所有的左子树节点</font>

循环进行k次后, 弹出的值就是第K小个节点

时间复杂度O(k + h),因为又可能加入最左列的时间复杂度h大于k

 空间复杂度O(h), 无论什么时候stack中都是没有在同一层的两个点的, 所以是一直往下的, O(h)

### Follow up: 如果二叉树经常被修改怎么办?

用类似QuickSelect的思想, 如果k大于左子树节点个数, 那k肯定在右子树, 减少一半的时间复杂度

具体做法: 

+ 可以在TreeNode中增加一个counter, <font color = blue>代表该节点的左子树一共多少个节点</font>
+ 也可以用一个HashMap<TreeNode, Integer>来记录<font color = blue>该节点的左子树一共多少个节点</font>

这样写出来的话时间复杂度仅为O(h), <font color = red>因为每次判断k和左子树节点个数时, 节点都会往下一层, 最多走O(h)层</font>

+ k > 左子树节点个数 + 1, k一定在右边, root = root.right, **k = k - 1 - 左子树个数**
+ k = 左子树节点个数 + 1, k就是当前root, k =root.val
+ k <= 左子树节点个数, k在左边, root = root.left, **k不变**

但是查找左子树节点个数需要先用一个分治法来计算, 这一条的时间复杂度就已经是O(n)了, <font color = red>好处是如果改变node的值, 这个HashMap依然好用</font>

<font color = red>**因此总的时间复杂度其实是O(n), 但是如果光看查找这一步的话是O(h),多次查找更占优势**</font>



# 题解
时间复杂度O(k + h), 空间复杂度O(h)

```java
public int kthSmallest(TreeNode root, int k) {
    // 声明stack
    Stack<TreeNode> stack = new Stack<TreeNode>();
    // 加入最左列
    findMostLeft(root, stack);
    // 开始遍历
    TreeNode node = null;
    for (int i = 0; i < k; i++) {
        node = stack.pop();
        // 如果存在右节点, 就把它和它所有左子节点都加进来
        if (node.right != null) {
            findMostLeft(node.right, stack);
        }
    }
    return node.val;

}
// 把当前点加入stack并且将其所有左子节点都加入stack
private void findMostLeft(TreeNode node, Stack<TreeNode> stack) {
    while (node != null) {
        stack.push(node);
        node = node.left;
    }
}
```

### 适合多次查找的QuickSelect思想

填写HashMap时间复杂度O(n),查找时间复杂度O(h)

```java
public int kthSmallest(TreeNode root, int k) {

    // 首先创建一个HashMap来记录
    Map<TreeNode, Integer> map = new HashMap<TreeNode, Integer>();
    // 用O(n)的方法给每个点的属性加上其所有的左子节点
    countNodes(root, map);
    // 用快速选择的思想做, 时间复杂度O(h), 最多走h层
    return QuickSelect(root, k, map);

}

private int countNodes(TreeNode root, Map<TreeNode, Integer> map) {
    if (root == null) {
        return 0;
    }
    int left = countNodes(root.left, map);
    int right = countNodes(root.right, map);
    // 整体相加
    map.put(root, left + right + 1);
    return left + right + 1;
}

private int QuickSelect(TreeNode root, int k, Map<TreeNode, Integer> map) {
    if (root == null) {
        return -1;
    }
    // 左子树总节点数
    int left = root.left == null ? 0 : map.get(root.left);
    if (k > left + 1) {
        return QuickSelect(root.right, k - left - 1, map);
    }
    if (k == left + 1) {
        return root.val;
    }
    return QuickSelect(root.left, k, map);
}
```

