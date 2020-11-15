# 题目地址

https://www.lintcode.com/problem/subsets-ii



# 题目描述

Given a collection of integers that might contain duplicates, *nums*, return all possible subsets (the power set).

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
Input: [1,2,2]
Output:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

### Challenge

Can you do it in both recursively and iteratively?

### Notice

- Each element in a subset must be in *non-descending* order.
- The ordering between two subsets is free.
- The solution set must not contain duplicate subsets.



# 思路

因为题目要求找出所有可能的子集,所以肯定是要用搜索, 本题可用DFS

重点在于本题需要<font color = red>去重, 不是指[1,2,2]和[2,2,1]这样的去重, 而是指[2]和[2]这样的去重!</font>

注意: 不能先把所有的可能性都写出来再去重, 这样的话会直接O(2<sup>n</sup>), 耗费过多

主题思维和[Subset](/Users/parallax/Documents/CS/LintCode/0017 SubSets.md) 一模一样, 选择的是第二种思维来作为basis的

关于去重: <font color = red>当传入的数在它之前出现过, **并且当前index不是startIndex时,直接continue掉**</font>

**例如:**

[2,2,2,3,4,4,5,6], 对于第一次[2,2], 通过dfs获得所有以[2,2]开头的subsets

但是第二次出现[2,2]时,startIndex还是1, 但是i已经走过了第一个[2,2], 变成了2 > startIndex,

<font color = blue>就说明剩下出现的全都是重复的,可以直接跳过选择[2,3]了</font>

<font color = red>而对于[2,4,4]这样的时候, 虽然4 = 4, 但是i就是startIndex, 所以不会跳过</font>

**怎么实现?**

只要**原数组上一位**nums[i -1]和当前数字相同(<font color = red>当传入的数在它之前出现过</font>)

并且**当前**i > startIndex(<font color = red>比如[1,1,1]中第二次[1,1]时, startIndex = 1, i = 2</font>)

直接continue



# 题解

时间复杂度O(n * 2<sup>n</sup>), 空间复杂度为O(n * 2<sup>n</sup>),<font color = blue>但是其实n * 2<sup>n</sup>和2<sup>n</sup>差不多, 所以也可以是O(2<sup>n</sup>)</font>

```java
public List<List<Integer>> subsetsWithDup(int[] nums) {

    List<List<Integer>> lists = new ArrayList<List<Integer>>();
    // null case
    // 此处nums.length == 0并不能写在一起, 因为存在[]可以返回, 不是null
    if (nums == null) {
        return lists;
    }
    // 内部为快排, 时间复杂度O(nlogn)
    Arrays.sort(nums);

    List<Integer> list = new ArrayList<Integer>();
    subsetsHelper(nums, 0 ,list, lists);
    return lists;
}


private void subsetsHelper(int[] nums, int startIndex, List<Integer> list, List<List<Integer>> lists) {

    // 每次进来的值都是加入的
    lists.add(new ArrayList<Integer>(list));

    // 首先要确保list必须是非降的
    // 每次都从可以开始选的startIndex开始, 避免出现[1,3,2]的情况
    for (int i = startIndex; i < nums.length; i++) {

        //当传入的数在它之前出现过, 并且当前index不是startIndex时,直接continue掉
        if (i > startIndex && nums[i - 1] == nums[i]){
            continue;
        }

        // 加入当前值
        list.add(nums[i]);
        // 要保持思维, 知道这一步就能让现在list的所有可能性都加进去了
        subsetsHelper(nums, i + 1, list, lists);
        // 进入下个loop之前, 要删除当前的那个值
        // 回溯
        list.remove(list.size() - 1);
    }
}
```
