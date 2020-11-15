# 题目地址

https://www.lintcode.com/problem/implement-strstr



# 题目描述

For a given source string and a target string, you should output the **first** index(from 0) of target string in source string.

If target does not exist in source, just return `-1`.

### Example

**Example 1:**

```
Input: source = "source" ，target = "target"
Output: -1	
Explanation: If the source does not contain the target content, return - 1.
```

**Example 2:**

```
Input:source = "abcdabcdefg" ，target = "bcd"
Output: 1	
Explanation: If the source contains the target content, return the location where the target first appeared in the source.
```

### Challenge

O(n^2^) is acceptable. Can you implement an O(n) algorithm? (hint: *KMP*)





# 思路

用双指针

+ source 和 target 有一个null, 就直接return null即可
+ source 为`""` 而target为 `""` , return 0

+ source不为 `""` 而target 为 `""` , return 0
+ Source为 `""` 而target不为 `""` ,return -1

主体部分双指针遍历,外层对于source每一个char, 内层对应target.





# 题解

### 双指针

时间复杂度O(n<sup>2</sup>),空间复杂度O(1)

```java
public int strStr(String source, String target) {

    if (source == null || target == null){ // 首先排除null case
        return -1;
    }

    if (target.equals("")) { // 对target的进行检测,无论source是什么都成效
        return 0;
    }

    if (source.equals("") && !target.equals("")) { // source和target都写清楚就不用和上面分清顺序
        return -1;
    }


    for (int i = 0; i < source.length() - target.length() + 1; i++) { // 考虑到IndexOutOfBound的i取值
        int j = 0;
        for (j = 0; j < target.length(); j++) { // 对target遍历
            if (source.charAt(i + j) != target.charAt(j)) { // 减少缩进量
                break;
            }
        }

        if (j == target.length()) { // 出循环后如果j等于长度,那必然是走完了
            return i;
        }
    }

    return -1; // 之前没return而走到这就是不存在
}
```

