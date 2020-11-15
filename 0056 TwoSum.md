# 题目地址

https://www.lintcode.com/problem/two-sum



# 题目描述

Given an array of integers, find two numbers such that they add up to a specific target number.

The function `twoSum` should return *indices* of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are zero-based.

### Example

```
Example1:
numbers=[2, 7, 11, 15], target=9
return [0, 1]
Example2:
numbers=[15, 2, 7, 11], target=9
return [1, 2]
```

### Challenge

Either of the following solutions are acceptable:

- O(n) Space, O(nlogn) Time
- O(n) Space, O(n) Time

### Notice

You may assume that each input would have exactly one solution



# 思路

普通思路: 双层遍历, 第一层i从0到n, 第二层j从i+1到n, 但这样时间复杂度为O(n<sup>2</sup>),达不到要求

### HashSet

这里要用到<font color = blue>hash table</font>的知识

HashMap的查找操作只需要O(1)的时间消耗

一旦HashMap中存在target - number的话就直接返回

<font color = red>这种做法仅对于两个值有效,如果是three sum则不一定</font>



### 排序+双指针

首先对输入的array进行sort,<font color = blue>increasing</font>

接着双指针分别指向第一个和最后一个元素

如果 `arr[left] + arr[right] > target `, <font color = blue>right必须向左移动</font>

如果 `arr[left] + arr[right] < target `, <font color = blue>left必须向右移动</font> 

用一个while套着if和else if保证每个循环移动一次,直到找到对应的left和right

如果left直接等于了right,还没找到的话就直接出循环



# 题解

### HashMap

时间复杂度O(n)(<font color = blue>进行n次遍历,每次查找为O(1)</font>), 空间复杂度O(n)(<font color = blue>存储n个int的hashSet</font>)

```java
public int[] twoSum(int[] numbers, int target) {
    // write your code here

    int [] two = new int[2];
    Map<Integer, Integer> map = new HashMap<Integer, Integer>(); // HashSet不方便查找index,需要用map
	
    for (int i=0;i<numbers.length;i++){
		// 只需一次遍历,如果存在target - numbers[i], 那必然可以达到target
        if (map.containsKey(target - numbers[i])) {
            two[0] = map.get(target - numbers[i]);
            two[1] = i;
            return two;
        }
		// 分别存储对应的值和其index
        map.put(numbers[i], i);
    }
    return two;
}
```



### 排序+双指针

时间复杂度O(nlogn),空间复杂度O(n)

<font color = green>时间复杂度来自于Array.sort算法,本质为快速排序</font>

空间复杂度来自于存储Pair的array,<font color = blue>如果本题只需要返回值而不是index则空间复杂度为O(1)</font>

```java
class Pair{ // 给出pair class
    int value;
    int index;
    public Pair(int value, int index) {
        this.value = value;
        this.index = index;
    }
    public int getValue() { // 严谨一点都用get
        return this.value;
    }
    public int getIndex() {
        return this.index;
    }
}
```

本体

```java
public int[] twoSum(int[] numbers, int target) {
    // write your code here

    Pair[] pairs = new Pair[numbers.length]; // 创建一个pair的array,空间复杂度O(n)
    for (int i = 0; i < numbers.length; i++) {
        pairs[i] = new Pair(numbers[i], i); // 以value和index存储
    }

    Arrays.sort(pairs, new Comparator<Pair>() {
        @Override
        public int compare (Pair p1, Pair p2) { // Arrays comparator 进行sort
            return p1.getValue() - p2.getValue(); // 从低到高
        }
    });

    int left = 0, right = numbers.length - 1;
    while (left < right) { 
        if (pairs[left].getValue() + pairs[right].getValue() > target) { // 由于是if所以不用再加上left<right
            right--;
        }else if (pairs[left].getValue() + pairs[right].getValue() < target) { // 用else if 
            left++;
        }else {
            int index1 = pairs[left].getIndex(); 
            int index2 = pairs[right].getIndex();
            int[] two = {index1, index2};
            Arrays.sort(two); // 直接用不带comparator的sort使其increasing排序
            return two;
        }
    }
    int[] two = {-1, -1};
    return two;
}
```

