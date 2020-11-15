# 题目地址

https://www.lintcode.com/problem/closest-binary-search-tree-value-ii



# 题目描述

Given a non-empty binary search tree and a target value, find `k` values in the BST that are closest to the target.

### Example

**Example 1:**

```
Input:
{1}
0.000000
1
Output:
[1]
Explanation：
Binary tree {1},  denote the following structure:
 1
```

**Example 2:**

```
Input:
{3,1,4,#,2}
0.275000
2
Output:
[1,2]
Explanation：
Binary tree {3,1,4,#,2},  denote the following structure:
  3
 /  \
1    4
 \
  2
```

### Challenge

Assume that the BST is balanced, could you solve it in less than O(n) runtime (where n = total nodes)?

### Notice

- Given target value is a floating point.
- You may assume `k` is always valid, that is: `k ≤ total` nodes.
- You are guaranteed to have only one `unique` set of k values in the BST that are closest to the target.



# 思路

### 方法一

中序递归遍历BST,得到increasing sequence, 然后遍历数组找到第一个大于target的值的index

+ 如果index >= length, 说明每个数都比target小, 直接返回list.subList(n - k, n);

+ 如果index < length, **用背向双指针**, left = i - 1, right = i, 只需要写出一边的条件, 循环k次

  `if (left >= 0 && (right >= n || target - list(left) < list(right) - target) ` , result.add(list(left)), left--

  <font color = blue>左指针不出界&&(右指针出界 or 左边值更接近target)</font>

  else, result.add(list(right)), right++

+ 时间复杂度O(n), 空间复杂度O()



### 方法二

参考[Closest Binary Search Tree Value](/Users/parallax/Documents/求职/LintCode/0900 Closest Binary Search Tree Value.md)

直接对整棵树遍历, 然后找到离K最近的两个值(<font color = blue>一个大于一个小于</font>)

然后用双指针向两侧遍历k次

难点在于怎么用logn的时间就找到离K最近的值 , 但本题给的是**平衡二叉树, 有操作空间**

+ 首先用 <font color = blue>next 和 prev </font>两个stack存储<font color = blue>大于和小于</font>target的节点

  不难看出, prev递增, next递减, 符合从中间往两边

  ```java
  while (node != null) {
      if (node.val < target) {
          next.push(node);
          node = node.right;
      } else {
          prev.push(node);
          node = node.left;
      }
  }
  ```

  这样的代码可以直接找出<font color = red>**大于target的最小值和小于target的最大值 **且这个stack中数量也很可能不等于K</font>

  这里的upper和lower一定是大于target和小于target里面最接近的, <font color = blue>但除了它们之外的点并不一定</font>最接近, 

  <font color = red>因为对于每个node而言,下一个点首先是要看下右节点的所有左子树/左节点的所有右子树, 然后再看最近一个通过左节点包括当前node的节点/最近一个通过右节点包括当前node的节点,**其他stack里的点恰好是这个关系**</font>

+ 当**返回数组result(不是stack)**的size没有达到k时, 比较next 和 prev 最上面两个数(举例 4 和 5, target = 4.5)

  + 如果next更接近, 就加入result**尾部,** 然后<font color = blue>取node.right加入, 并且把它所有的左子树加入stack</font>, 如 5被弹出后加入`[10,8,7,6]`, 保持递减
  
    **如果node.right == null, 那就直接走stack的上一步,也就是最近一个通过左节点包括当前node的节点**
  
  + 如果prev更接近, 就加入result**头部,** 然后<font color = blue>取node.left加入, 并且把它所有的右子树加入stack</font>, 如 4被弹出后加入`[1,2,3]`, 保持递减

<font color = red>如果prev或者next为空, 说明已经没有更小or更大的值了, 直接给一个Double.MAX, 保证大于任何距离</font>

 时间复杂度 O(K + logn), <font color = blue>初始化stack为logn</font>

<font color = red>对于左右指针拉开部分, 外层循环k次,内层goPrev走满最坏也只有logn, goNext走满也是logn, **因此时间复杂度为O(K + logn)**</font>

空间复杂度O(logn),<font color = blue>来自两个stack, 最多的情况下两个stack都是从root到最深层, 也只有2logn</font>

值得多回味





# 题解

### 遍历

时间复杂度O(n), 空间复杂度O(n)

```java
public List<Integer> closestKValues(TreeNode root, double target, int k) {
    // write your code here
    List<Integer> list = new ArrayList<Integer>();
    traverse(root, list);

    // 找到第一个大于target的值的index
    int i;
    for (i = 0; i < list.size(); i++) {
        if (list.get(i) >= target) {
            break;
        }
    }
    // 左右指针
    int left = i - 1, right = i;
    List<Integer> result = new ArrayList<Integer>();
    for (int j = 0; j < k; j++) {
        if (left >= 0 && (right >= list.size() || target - list.get(left) <list.get(right) - target)){
            result.add(list.get(left));
            left--;
        } else {
            result.add(list.get(right));
            right++;
        }
    }
    return result;
}
private void traverse(TreeNode root, List<Integer> list) {
    if (root == null) {
        return;
    }
    traverse(root.left, list);
    list.add(root.val);
    traverse(root.right, list);
}
```

### 非常规: 非递归遍历

时间复杂度O(logn + k),<font color = blue>初始stack为logn, goPrev和goNext最坏为logn左右拉开为k, 所以最差为K * logn</font>

空间复杂度O(logn),<font color = blue>来自两个stack, 最多的情况下两个stack都是从root到最深层, 也只有2logn</font>

```java
public List<Integer> closestKValues(TreeNode root, double target, int k) {
    // 声明prev和next
    Stack<TreeNode> prev = new Stack<TreeNode>();    
    Stack<TreeNode> next = new Stack<TreeNode>();
    TreeNode node = root;
    // 找出target前后两个数
    while (node != null) {
        if (node.val < target) {
            prev.push(node);
            node = node.right;
        } else {
            next.push(node);
            node = node.left;
        }
    }

    List<Integer> result = new ArrayList<Integer>();
    // 循环条件是result长度没到k
    while (result.size() < k) {
        // 求出最近的两个距离
        // 如果prev或者next空了就设置最小or最大值,保证距离更远
        // 用Double的最大最小更好, 用Integer不能保证距离更远
        double distancePrev = prev.isEmpty() ? Double.MAX_VALUE : Math.abs(target - prev.peek().val);
        double distanceNext = next.isEmpty() ? Double.MAX_VALUE : Math.abs(target - next.peek().val);

        // 比较next 和 prev 最上面两个数
        // 如果next更接近, 取node.right并且把它所有的左子树加入stack
        // 如果prev更接近, 取node.left并且把它所有的右子树加入stack
        if (distanceNext < distancePrev) {
            result.add(next.peek().val);
            goNext(next);
        } else {
            result.add(0, prev.peek().val);
            goPrev(prev);
        }
    }
    return result;
}

// 寻找整棵树中大于当前节点的最小的点
private void goNext(Stack<TreeNode> next) {
    TreeNode node = next.pop().right;
    while (node != null) {
        next.push(node);
        node = node.left;
    }
}
// 寻找整棵树中小于当前节点的最大的点
private void goPrev(Stack<TreeNode> prev) {
    TreeNode node = prev.pop().left;
    while (node != null) {
        prev.push(node);
        node = node.right;
    }
}
```

