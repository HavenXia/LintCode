# 题目地址

https://www.lintcode.com/problem/kth-largest-element



# 题目描述

Find K-th largest element in an array.

### Example

**Example 1:**

```
Input:
n = 1, nums = [1,3,4,2]
Output:
4
```

**Example 2:**

```
Input:
n = 3, nums = [9,3,2,4,8]
Output:
4
```

### Challenge

O(n) time, O(1) extra memory.

### Notice

You can swap elements in the array



# 思路

第一反应可能是用归并排序和快速排序,但是实际上O(nlogn)的时间复杂度还能被优化

使用Quick Select只有O(n), (<font color = blue>不需要知道怎么证明</font>)

当 `i <= j && nums[i] > pivot` 时, i需要往右一步

当 `i <= j && nums[j] < pivot` 时,j需要往左一步

可能出现两种情况

+ 被分成 `{1,2,3,4}`  `{5,6,7,8}` ,然后按左右分区递归, 递归到最后出现 `start == end` , 返回该值
+ 被分成 `{1,2,3}` `{4} ` `{5,6,7,8}` ,如果在左右分区依旧相同,如果正好k处在中间位置则直接返回<font color = blue>nums[j + 1]</font> 





# 题解

时间复杂度O(n),空间复杂度O(1)

```java
private int quickSelect(int[] nums, int start, int end, int k) {

    if (start == end) { // 首先设置出口
        return nums[start];
    }

    int i = start, j = end; 
    int pivot = nums[(i + j) / 2]; // 选择pivot

    while (i <= j) {
        while (i <= j && nums[i] > pivot) { // 同样的左右交换,不过变成了大于的才换过去
            i++;
        }
        while (i <= j && nums[j] < pivot) {
            j--;
        }
        if (i <= j) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
            i++;
            j--;
        }
    }

    if (start + k - 1 <= j) {  // 每次交换完确保k在那一段
        return quickSelect(nums, start, j, k);    
    }
    if (start + k - 1 >= i) { // k在右区间时k会随之改变
        return quickSelect(nums, i ,end, k - (i - start));
    }
    return nums[j + 1]; // 仅在 i和j中间还有一位时生效

}
```





