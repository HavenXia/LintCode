# 题目地址

https://www.lintcode.com/problem/permutations



# 题目描述

Given a list of numbers, return all possible permutations.

### Example

**Example 1:**

```
Input: [1]
Output:
[
  [1]
]
```

**Example 2:**

```
Input: [1,2,3]
Output:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

### Challenge

Do it without recursion.

### Notice

You can assume that there is no duplicate numbers in the list.



# 思路

需要求的是所有的排列, 一共有A(n,n)种排列

没有重复的数, 而且因为是排列问题,<font color = blue>不存在需要非降顺序的需求</font>

依旧可以用dfs, 进入dfs后, 对index从0 到length - 1遍历(<font color = blue>不能和subsets一样从startIndex开始, 这里是可以出现[2,1,3]的</font>)

依旧先是[1], 然后递归dfs把[1]开头的所有排列都走完

<font color = red>在[1]的所有排列都递归完之后, 因为要开始[2]开头的排列, 需要回溯, 删除掉当前list中的1</font>

<font color = blue>为了保证没有重复的数字,创建一个boolean数组visited, 按照对应的index存储每个数是否已经在list中了, 同样需要回溯</font>

时间复杂度:O(n * n!),<font color = blue>一共有n!个可能性, 每次构造都要花O(n)来复制list</font>

空间复杂度: O(n * n!), <font color = blue>存储空间一共用到n!个list, 每个长度为n </font> 



# 题解

时间复杂度:O(n * n!),空间复杂度:O(n * n!)

```java
public List<List<Integer>> permute(int[] nums) {
    // write your code here
    List<List<Integer>> lists = new ArrayList<List<Integer>>();
    // null case
    // 此处nums.length == 0并不能写在一起, 因为存在[]可以返回, 不是null
    if (nums == null) {
        return lists;
    }
    // 寻找以list为开头的所有排列
    dfs(nums, new boolean[nums.length], new ArrayList<Integer>(), lists);
    return lists;
}

// 寻找以list为开头的所有排列方法
private void dfs(int[] nums, boolean[] visited, List<Integer> list, List<List<Integer>> lists) {

    // 当前list的size达到nums的长度时, 说明一个排序已经走到底了, 直接加入复制
    if (list.size() == nums.length) {
        lists.add(new ArrayList<Integer>(list));
        return;
    }

    // i从0开始循环
    for (int i = 0; i < nums.length; i++) {
        // 如果访问过, 就直接continue
        if (visited[i] == true) {
            continue;
        }

        // 否则就直接加入
        list.add(nums[i]);
        // visited要修改为true
        visited[i] = true;
        // 进入dfs, 寻找以list为开头的所有排列
        dfs(nums, visited, list, lists);
        // dfs结束后要回溯, 进入下个i之前删掉当前的i和修改visited
        visited[i] = false;
        list.remove(list.size() - 1);
    } 
}
```

