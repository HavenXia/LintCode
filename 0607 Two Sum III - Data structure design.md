# 题目地址

https://www.lintcode.com/problem/two-sum-iii-data-structure-design



# 题目描述

Design and implement a TwoSum class. It should support the following operations: `add` and `find`.

`add` - Add the number to an internal data structure.
`find` - Find if there exists any pair of numbers which sum is equal to the value.

### Example

**Example 1:**

```
add(1); add(3); add(5);
find(4) // return true
find(7) // return false
```





# 思路

此题需要两个功能,分别是add和find

<font color = blue>add只需要加入元素即可, find需要是否有两个元素之和为target</font>

### 方法一:

如何一边增加数一边保持数组有序:

+ add功能需保持一个有序数组——ArrayList.add <font color = red>O(n)</font> 和Collections.sort  <font color = red>O(nlogn)</font>, **<font color = blue>太慢了</font>** 

+ Binary Search(二分) + ArrayList.add,add时用二分法查找位置<font color = red>O(logn)</font>, 但是ArrayList.add <font color = red>O(n)</font> 依旧是瓶颈(因为要让剩下元素移动一位), 使用二分法反而还会增加<font color = red>编程复杂度</font>

  <font color = blue>比如在[1,2,3,4,5]中插入0</font>

+ 不能使用Linked List, 虽然linked list 实现add可以做到<font color = red>O(1)</font>(直接previous.next和this.next), 但是linked list的<font color = blue>index access是O(n)</font>的(给定index), 这样会导致二分法根本做不到<font color = red>O(logn)</font>

综上,方法一为:

**Insertion Sort O(n)**

### 方法二:

用HashTable, 在这里可以用HashMap

每add一个number, 如果尚未contains, put进map并计数,否则计数+1 <font color = red>O(1)</font>

查找TwoSum直接对HashMap遍历 <font color = red>O(n)</font>

+ 如果 target - 当前值  ! = 当前值 并且 map中存在,return true
+ 如果 target = 两倍当前值并且 当前值计数>1, return true







# 题解

### Add - Insertion sort O(n)

### Find - 双指针 O(n)

<font color = red>**超时!! 仅仅列出来用于参考**</font>

```java
public class TwoSum {
    // 设置variable
    public List<Integer> nums;
    // 构造函数
    public TwoSum() {
        nums = new ArrayList<Integer>();
    }
    public void add(int number) {
        // 先加入元素
        nums.add(number);
        int index = nums.size() - 1;
        // Insertion Sort, 从最后一个向前比
        while (index > 0) { // O(n), 且避免了只有一个元素的情况
            if (nums.get(index) >= nums.get(index - 1)) {
                break;
            }
            int temp = nums.get(index); // 记得用set
            nums.set(index,nums.get(index - 1));
            nums.set(index - 1,temp);
            index--;
        }
    }
    // 查找函数
    public boolean find(int value) {
        // null case
        if (nums == null || nums.size() == 0) {
            return false;
        }
        int start = 0, end = nums.size() - 1;
        // 标准的TwoSum查找
        while (start < end) {
            // 找到直接return true
            if (nums.get(start) + nums.get(end) == value) {
                return true;
            }
            if (nums.get(start) + nums.get(end) > value) {
                end--;
            }
            if (nums.get(start) + nums.get(end) < value) {
                start++;
            }
        }
        return false;
    }
```



### HashTable

add 时间复杂度O(1), find时间复杂度O(n)

```java
public class TwoSum {
    // 创建map
    public Map<Integer,Integer> map;
    int length;
    // 构造
    public TwoSum() {
        map = new HashMap<Integer,Integer>();
    }
    // O(1)
    public void add(int number) {
        // put value以及出现次数
        if (!map.containsKey(number)) {
            map.put(number, 0);
        }
        map.put(number, map.get(number) + 1);
    }
    
    // O(n)
    public boolean find(int value) {
        // 也可以用map.isEmpty()
       if (map == null || map.size() == 0) {
           return false;
       }
       // 遍历
       for (int number: map.keySet()) {
           // 不相等且存在
           if (number != value - number && map.containsKey(value - number)) {
               return true;
           }
           // 相等且有两个以上
           if (number == value - number && map.get(number) > 1) {
               return true;
           }
       }
       return false;
    }
}
    
    
```

