# 题目地址

https://www.lintcode.com/problem/binary-tree-paths



# 题目描述

Given a binary tree, return all root-to-leaf paths.

### Example

**Example 1:**

```
Input：{1,2,3,#,5}
Output：["1->2->5","1->3"]
Explanation：
   1
 /   \
2     3
 \
  5
```

**Example 2:**

```
Input：{1,2}
Output：["1->2"]
Explanation：
   1
 /   
2  
```





# 思路

普通的递归

设置出口为

+ root == null
+ root.left == null && root.right == null, 说明当前path走到头了, result.add(root)
+ 否则就按照left和right分别存在的情况下递归

<font color = red>此题是没有用到backtracking的,但是实际上可以把path先修改之后再改回来</font>

此处的空间开辟其实是很浪费的, <font color = blue>所有遍历一共开辟了1+2+...+n的string空间</font>

**如何优化?**

使用backtracking的技巧,全部递归用同一个path,此处的path是stringBuilder

这样的话空间复杂度就只有O(n), <font color = blue>即占用最长队列的时候最多也只有n</font>



# 题解

时间复杂度O(n), 所有节点都走了一遍

空间复杂度O(n<sup>2</sup>),<font color = blue>所有遍历一共开辟了1+2+...+n的string空间</font>

```java
List <String> list = new ArrayList<String>();
public List<String> binaryTreePaths(TreeNode root) {
    // write your code here
    if (root != null){
        traverse(root,Integer.toString(root.val));
    }
    return list;
}

private void traverse(TreeNode root,String path){
    if (root == null){
        return;
    }else if (root.left == null && root.right == null){
        list.add(path);
    }else{
        if (root.left != null){
            traverse(root.left,path + "->" + Integer.toString(root.left.val));
        }
        if (root.right != null){
            traverse(root.right,path + "->" + Integer.toString(root.right.val));
        }
    }
}
```

### backtracking优化后

```java
List <String> list = new ArrayList<String>();
public List<String> binaryTreePaths(TreeNode root) {
    // write your code here
    if (root != null){
        StringBuilder path = new StringBuilder();
        path.append(Integer.toString(root.val));
        traverse(root, path, list);
    }
    return list;
}

private void traverse(TreeNode root,StringBuilder path, List<String> list){

    int oldPathlength = path.length();
    if (root == null){
        return;
    }else if (root.left == null && root.right == null){
        list.add(path.toString());
    }else{
        if (root.left != null){
            path.append("->" + Integer.toString(root.left.val));
            traverse(root.left,path, list);
            path.delete(oldPathlength, path.length());

        }
        if (root.right != null){
            path.append("->" + Integer.toString(root.right.val));
            traverse(root.right,path, list);
            path.delete(oldPathlength, path.length());
        }
    }
}
```

