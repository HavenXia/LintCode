# 题目地址

https://www.lintcode.com/problem/closest-binary-search-tree-value



# 题目描述

Given a non-empty binary search tree and a target value, find the value in the BST that is closest to the target.

### Example

**Example1**

```
Input: root = {5,4,9,2,#,8,10} and target = 6.124780
Output: 5
Explanation：
Binary tree {5,4,9,2,#,8,10},  denote the following structure:
        5
       / \
     4    9
    /    / \
   2    8  10
```

**Example2**

```
Input: root = {3,2,4,1} and target = 4.142857
Output: 4
Explanation：
Binary tree {3,2,4,1},  denote the following structure:
     3
    / \
  2    4
 /
1
```

### Notice

- Given target value is a floating point.
- You are guaranteed to have only one unique value in the BST that is closest to the target.

Input test data (one parameter per line)How to understand a testcase?



# 思路

用非递归的方法遍历BST

详见[BSTIterator](/Users/parallax/Documents/CS/九章算法课/07 递归,DFS,回溯.md)

代码几乎一样, 但是多出一个closest变量, 一旦发现差值的abs最小, 就记录下这个node.value

时间复杂度O(n), 空间复杂度O(h), <font color = blue>因为BST不是满的树, 最差情况就是O(h)都在stack里</font>

### 有没有更快的方法?(<font color = red>牢记并背诵</font>)

对于BST可以用寻找最近的upper and lower bound, 可以直接获得离target最近的两个点

<font color = red>upper和lower初始化必须和root一样(**避免自己设定的初始值更接近**),并且最后比较一定要用abs! (**避免由于是root的值而出现负数**)</font>

+ 如果当前node的值大于target, 把这个node赋值给上限upper, 然后node = node.left(<font color = blue>寻找更小点</font>)
+ 如果当前node的值小于target, 就把这个node赋值给下限lower, 然后node = node.right(<font color = blue>寻找更大点</font>)
+ 如果当前node的值等于target, 直接返回它即可

**<font color = red>在以while (node != null)遍历到头后,得到的不一定是最接近target的两个值!!!!!!!!!!</font>**

**实际上, 应该是出循环后, 保存下来的upper 和lower是大于和小于trarget的值的最小/最大值!!!!**

<font color = blue>尽管它们不一定是最接近的, 比如[10,4,5], target =5.5, 最接近的是4和5, 但是5存在的时候不用考虑4, 只要去找大于target的最小值即可</font>

这个方法非常elegant, 需要牢记并背诵!!! 时间复杂度为O(h), 空间复杂度为O(1)





# 题解

先把BST非递归遍历写成class内class

```java
class BSTIterator {    
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

在进行iterator, 因为是从小到大的, 所以比target大的情况下**只要检测第一个值就够了**

因此在这里先进行比较closest,再判断是否大于,这样确保第一个大于target的数检测到了

```java
public int closestValue(TreeNode root, double target) {

    double closest = Math.abs(target - root.val);
    TreeNode closestNode = root;

    BSTIterator bst = new BSTIterator(root);
    while (bst.hasNext()) {
        TreeNode node = bst.next();
        if (Math.abs(target - node.val) < closest) {
            closest = Math.abs(target - node.val);
            closestNode = node;
        }
        if (node.val > target) {
            break;
        }
    }
    return closestNode.val;
}
```

### 更快的方法: 上下限法

时间复杂度O(h),空间复杂度O(1)

```java
public int closestValue(TreeNode root, double target) {

    TreeNode upper = root, lower = root;

    while (root != null) {
        if (root.val > target) {
            upper = root;
            root = root.left;
        } else if (root.val < target) {
            lower = root;
            root = root.right;
        } else {
            return root.val;
        }
    }

    if (Math.abs(upper.val - target) < Math.abs(lower.val - target)) {
        return upper.val;
    } 
    return lower.val;
}
```

