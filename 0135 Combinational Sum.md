# 题目地址

https://www.lintcode.com/problem/combination-sum



# 题目描述

### **Description**

Given a set of candidtate numbers `candidates` and a target number `target`. Find all unique combinations in `candidates` where the numbers sums to `target`.

The **same** repeated number may be chosen from `candidates` unlimited number of times.

### **Example**

**Example 1:**

```
Input: candidates = [2, 3, 6, 7], target = 7
Output: [[7], [2, 2, 3]]
```

**Example 2:**

```
Input: candidates = [1], target = 3
Output: [[1, 1, 1]]
```

**Properties:**

1. All numbers (including `target`) will be positive integers.
2. Numbers in a combination `a1, a2, … , ak` must be in non-descending order. (ie, `a1 ≤ a2 ≤ … ≤ ak`)
3. Different combinations can be in any order.
4. The solution set must not contain duplicate combinations.



# 思路

按照DFS的模板来做

+ 整体上还是combiantion, 但是因为需求的是<font color = red>非降序列, 所以需要startIndex</font>
+ 因为此处可以重复选择数字, 所以传递<font color = red>startIndex就是当前的index</font> 
+ 为了降低时间复杂度,要考虑到[2,2,3] = 7之后<font color = red>就直接回到第二位进行loop(**但是其实不剪枝居然也行!!**)</font>

时间复杂度为O(n * n<sup>target/min</sup>), 最多也就是这么多位数的n次

空间复杂度O( n<sup>target/min</sup>),来自返回数组



# 题解

```java
public List<List<Integer>> combinationSum(int[] candidates, int target) {
    // write your code here

    Arrays.sort(candidates);

    List<List<Integer>> result = new ArrayList<List<Integer>>();
    if (candidates == null || candidates.length == 0) {
        return result;
    }

    traverse(result, candidates, new ArrayList<Integer>(), target, 0, 0);
    return result;
}

private void traverse(List<List<Integer>> result, 
                      int[] candidates, 
                      List<Integer> list, 
                      int target, int sum, 
                      int startIndex) {

    for (int i = startIndex; i < candidates.length; i++) {

        if (i != 0 && candidates[i] == candidates[i - 1]) {
            continue;
        }

        list.add(candidates[i]);
        sum += candidates[i];

        // 每次sum大于等于target的时候
      	// 直接删掉当前位然后return, sum不用backtrack
        if(sum > target) {
            list.remove(list.size() - 1);
            return;
        } else if (sum == target) {
            result.add(new ArrayList(list));
            list.remove(list.size() - 1);
            return;
        } else {
            traverse(result, candidates, list, target, sum, i);
        }

        list.remove(list.size() - 1);
        sum -= candidates[i];
    }
}
```



