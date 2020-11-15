# 题目地址

https://www.lintcode.com/problem/subarray-sum



# 题目描述

Given an integer array, find a subarray where the sum of numbers is **zero**. Your code should return the index of the first number and the index of the last number.

### Example

**Example 1:**

```
Input:  [-3, 1, 2, -3, 4]
Output: [0, 2] or [1, 3].
Explanation: return anyone that the sum is 0.
```

**Example 2:**

```
Input:  [-3, 1, -4, 2, -3, 4]
Output: [1,5]	
```

### Notice

There is at least one subarray that it's sum equals to zero.



# 思路

初见会想到用普通的遍历方法, 一共会遍历n+ n-1 + n -2 +...+1次, 同时每次遍历用一个sum函数来累加, 总时间复杂度超过**O(n<sup>2</sup>)**, 超时!

接着想到用一个HashMap<Pair, Intger> 来存start,end 以及sum, 但是这样仅仅省了一个sum的时间, 本身还是需要遍历n+ n-1 + n -2 +...+1次的, 依旧是**O(n<sup>2</sup>)**, 超时!

**O(n)方法**

用一个HashMap<Intger, Intger> 来存<font color = red>preSum和index</font>, 这里的**sum是从0到当前位置的所有element的sum!** 称作preSum

遍历整个数组, 每次得到sum后检测**是否已经存在于map中了**, 如果存在了, 说明==从之前到现在有一段subarray肯定和为0!==

为了得到那段subarray, 取map.get(preSum) + 1, 就是start, 而i就是end

**注意:** 在开始循环前需要在map.put(0, -1), 确保直接出现preSum = 0的时候可以正常的得到start = -1+1 = 0!



# 题解

时间复杂度O(n), 空间复杂度O(1)

```java
public List<Integer> subarraySum(int[] nums) {
    // write your code here
    List<Integer> result = new ArrayList<Integer>();
    if (nums == null || nums.length == 0) {
        return result;
    }

    Map<Integer, Integer> map = new HashMap<Integer, Integer>();
    map.put(0, -1);
    int preSum = 0;

    for (int i = 0; i < nums.length; i++) {
        preSum += nums[i];
        if (map.containsKey(preSum)) {
            result.add(map.get(preSum) + 1);
            result.add(i);
            break;
        } else {
            map.put(preSum, i);
        }
    }
    return result;
}
```

