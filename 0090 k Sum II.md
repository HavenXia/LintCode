# 题目地址

https://www.lintcode.com/problem/k-sum-ii



# 题目描述

Given n unique postive integers, number k (1<=k<=n) and target.

Find all possible k integers where their sum is target.

### Example

**Example 1:**

```
Input: [1,2,3,4], k = 2, target = 5
Output:  [[1,4],[2,3]]
```

**Example 2:**

```
Input: [1,3,4,6], k = 3, target = 8
Output:  [[1,3,4]]	
```





# 思路

<font color = red>不需要剪枝, 普通的结束条件即可</font>

+ 如果 sum == target 且 list.size() == k, 直接加入result并返回, <font color = red>但是对于[1,2,5] = 8的情况下还是会去测[1,2,6]</font>
+ 如果sum > target 可以直接return,<font color = red>但是[1,6] > 6 之后还会检测[1,7]</font>
+ 如果list.size() > target 可以直接return

剩下的就是DFS模板了

时间复杂度O(n * 2<sup>n</sup>), <font color = red>因为每个数都可以选或者不选, 最多2<sup>n</sup>种可能</font>

空间复杂度O( 2<sup>n</sup>)





# 题解

时间复杂度O(n * 2<sup>n</sup>), <font color = red>因为每个数都可以选或者不选, 最多2<sup>n</sup>种可能</font>

空间复杂度O( 2<sup>n</sup>)

```java
public List<List<Integer>> kSumII(int[] A, int k, int target) {
    // write your code here
    List<List<Integer>> result = new ArrayList<List<Integer>>();
    if (A == null || A.length == 0) {
        return result;
    }

    Arrays.sort(A);
    traverse(result, A, new ArrayList<Integer>(), 0, 0, k, target);
    return result;
}

private void traverse(List<List<Integer>> result,
                      int[] A,
                      List<Integer> list,
                      int startIndex,
                      int sum,
                      int k,
                      int target) {

    if (sum == target && list.size() == k) {
        result.add(new ArrayList(list));
        return;
    }
    if (sum > target || list.size() >= k) {
        return;           
    }

    for (int i = startIndex; i < A.length; i++) {
        list.add(A[i]);
        sum += A[i];
        traverse(result, A, list, i + 1, sum, k, target);
        list.remove(list.size() - 1);
        sum -= A[i];
    }
}
```

