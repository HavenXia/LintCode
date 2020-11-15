# 题目地址

https://www.lintcode.com/problem/two-sum-unique-pairs



# 题目描述

Given an array of integers, find how many `unique pairs` in the array such that their sum is equal to a specific target number. Please return the number of pairs.

### Example

**Example 1:**

```
Input: nums = [1,1,2,45,46,46], target = 47 
Output: 2
Explanation:

1 + 46 = 47
2 + 45 = 47
```

**Example 2:**

```
Input: nums = [1,1], target = 2 
Output: 1
Explanation:
1 + 1 = 2
```





# 思路

### 方法一

使用HashTable的思想,用一个HashMap保存value和出现的次数 O(n)

分成两部分

+ 验证是否 `map.contains(target - value)`
  + `target - value != value` 且 **map里不包含value**, count++
  + `target - value == value` 且 **map.get(value) 必须正好等于1**, count++
+ 第二部分是常规的加入map部分
  + 如果不存在就是put
  + 存在就次数+ 1

### 方法二

先排序O(nlogn)

再用双指针O(n)

接着双指针分别指向第一个和最后一个元素

如果 `arr[left] + arr[right] > target `, <font color = blue>right必须向左移动</font>

如果 `arr[left] + arr[right] < target `, <font color = blue>left必须向右移动</font> 

如果 `arr[left] + arr[right] == target ` ,<font color = red>先进行left++,right--和count++</font>,接着

+ while (left < right) 并且 nums[right] == nums[right + 1]时, right会一直向左直到出现新数
+ while (left < right) 并且 nums[left] == nums[left + 1]时, left会一直向右直到出现新数

**先进行count++是避免最后[1,1]这样的计数反而得到0**

**由于是while,所以 left < right也是必须一直保持的条件**



# 题解

### HashMap 

时间复杂度O(n),空间复杂度O(n)

```java
public int twoSum6(int[] nums, int target) {
    // null case
    if (nums == null || nums.length == 0) {
        return 0;
    }
	// create map
    Map<Integer,Integer> map = new HashMap<Integer,Integer>();
    int count = 0;
	
    for (int i = 0; i < nums.length; i++) {
        if (map.containsKey(target - nums[i])) {
			// target - value != value 且map里不包含value, count++
            if (target - nums[i] != nums[i] && !map.containsKey(nums[i])) {
                count++;
            }
			// target - value == value 且 map.get(value) 必须正好等于1, count++
            if (target - nums[i] == nums[i] && map.get(nums[i]) == 1) {
                count++;
            }
        }
		// 常规的加入部分
        if (map.containsKey(nums[i])) {
            map.put(nums[i], map.get(nums[i]) + 1);
        } else {
            map.put(nums[i], 1);
        }
    }
    return count;
}
```



### 排序+双指针

时间复杂度

时间复杂度O(nlogn),空间复杂度O(1)

```java
public int twoSum6(int[] nums, int target) {
    // null case
    if (nums == null || nums.length == 0) {
        return 0;
    }

    Arrays.sort(nums);
    int count = 0;
    int start = 0, end = nums.length - 1;
    while (start < end) {
		
        if (nums[start] + nums[end] == target) {
            // 先把这些做了,可以避免outofbound
            count++;
            start++;
            end--;
            // 因为用到了while 所以start<end是必须确保的
            // 这里必须用while,防止下个loop中还是相等
            while (start < end && nums[end] == nums[end + 1]) {
                end--;
            }
            while (start < end && nums[start] == nums[start - 1]) {
                start++;
            }
        }
        if (nums[start] + nums[end] > target) {
            end--;
        }
        if (nums[start] + nums[end] < target) {
            start++;
        }
    }

    return count;
}
```

