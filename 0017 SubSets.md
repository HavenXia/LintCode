# 题目地址

https://www.lintcode.com/problem/subsets



# 题目描述

Given a set of distinct integers, return all possible subsets.

### Example

**Example 1:**

```
Input: [0]
Output:
[
  [],
  [0]
]
```

**Example 2:**

```
Input: [1,2,3]
Output:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

### Challenge

Can you do it in both recursively and iteratively?

### Notice

- Elements in a subset must be in *non-descending* order.
- The solution set must not contain duplicate subsets.



# 思路

本题是标准的隐式DFS

可以理解为<font color = red>一棵树</font>, 起始节点是空的array[]

然后按照原数组的每个元素来分层

**可以用recursion解决**

<font color = blue>第一层是要不要nums[0], 第二层是要不要nums[1]....以此类推, 直到nums的最后一层</font>

因为要求的是subsets中的元素是**非降**的,所以最开始要对nums进行排序,消耗O(nlogn)的时间复杂度

dfs(nums, index, list, lists), <font color = blue>每次对list加或者不加当前的nums[index]分成两种, 然后接着dfs(nums, index + 1, list, lists)</font>

记得在开始检测index 是否超过了nums.length, 是的话就加入到lists中, <font color = red>**但是注意这里要用复制当前list!不然会随着递归变化!!**</font>

这一步也适用于nums为[], 然后0 >= length, 直接返回[]

<font color = red>**注意: 必须先做list.add(nums[index]), 左侧递归, 再list.remove()然后右侧递归, 确保数字是非降的!!!**</font>

这里用到了回溯的技巧



**也可以用比较通用的DFS算法**

<font color = blue>这种思路是按照从[]开始, 到[1], 然后[1,2]和[1,2,3], remove 3 再remove 2, 加入3变成[1,3]</font>

思路更倾向于**选哪个数**而不是**选不选某个数**

+ 每次dfs时, i从startIndex开始: 最初[]时,startIndex为0, i从0开始,先是[1], 然后dfs, startIndex=>1, i从1开始, 第一种为[1,2], 第二种为[1,3]..... 

+ 当[1]开头的所有可能性都递归遍历完之后, 回到最初startIndex为0时, i => 1, 先是[2], 然后dfs, startIndex  => 2, i 从2开始,第一种为[2,3], 第二种为[2, 4]...



时间复杂度为O(n * 2<sup>n</sup>), <font color = blue>这里的n来自复制数组</font>

空间复杂度O(n * 2<sup>n</sup>), <font color = blue>但是其实n * 2<sup>n</sup>和2<sup>n</sup>差不多, 所以也可以是O(2<sup>n</sup>)</font>



# 题解

### Recursion

时间复杂度O(n * 2<sup>n</sup>)(<font color = blue> `new ArrayList<Integer>()` 来复制需要时间复杂度O(n)</font>)

空间复杂度O(n * 2<sup>n</sup>), <font color = blue>但是其实n * 2<sup>n</sup>和2<sup>n</sup>差不多, 所以也可以是O(2<sup>n</sup>)</font>

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> lists = new ArrayList<List<Integer>>();
    // null case 
    // 此处nums.length == 0并不能写在一起, 因为存在[]可以返回, 不是null
    if (nums == null) {
        return lists;
    }
    // 内部为快排, 时间复杂度O(nlogn)
    Arrays.sort(nums);

    List<Integer> list = new ArrayList<Integer>();
    dfs(nums, 0, list, lists);
    return lists;
}

private void dfs(int[] nums, int index, List<Integer> list,  List<List<Integer>> lists) {

    // 检测是否index超过了
    // 这一步也适用于nums为[], 然后0 >= length, 直接返回[]
    if (index >= nums.length) {
        lists.add(new ArrayList<Integer>(list));
        return;
    }
    // 分类递归
    // 选当前数字的情况
    list.add(nums[index]);
    dfs(nums, index + 1, list, lists);
    // 不选当前数字的情况
    // 这里的remove相当于回溯
    list.remove(list.size() - 1);
    dfs(nums, index + 1, list, lists);
}
```

### 更通用的DFS做法

时间复杂度O(n * 2<sup>n</sup>), <font color = blue>这里的n来自复制数组,</font>空间复杂度O(2<sup>n</sup>)

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> lists = new ArrayList<List<Integer>>();
    // null case 
    // 此处nums.length == 0并不能写在一起, 因为存在[]可以返回, 不是null
    if (nums == null) {
        return lists;
    }
    // 内部为快排, 时间复杂度O(nlogn)
    Arrays.sort(nums);

    List<Integer> list = new ArrayList<Integer>();
    dfs(nums, 0, list, lists);
    return lists;
}

private void dfs(int[] nums, int startIndex, List<Integer> list, List<List<Integer>> lists) {

    // 每次进来的值都是加入的
    lists.add(new ArrayList<Integer>(list));

    // 首先要确保list必须是非降的
    // 每次都从可以开始选的startIndex开始, 避免出现[1,3,2]的情况
    for (int i = startIndex; i < nums.length; i++) {
        // 加入当前值
        list.add(nums[i]);
        // 要保持思维, 知道这一步就能让现在list的所有可能性都加进去了
        dfs(nums, i + 1, list, lists);
        // 进入下个loop之前, 要删除当前的那个值
        // 回溯
        list.remove(list.size() - 1);
    }
}
```

