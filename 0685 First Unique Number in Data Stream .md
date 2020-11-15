# 题目地址

https://www.lintcode.com/problem/first-unique-number-in-data-stream



# 题目描述

Given a continuous stream of data, write a function that returns the first unique number (including the last number) when the terminating number arrives. If the unique number is not found, return `-1`.

### Example

**Example1**

```
Input: 
[1, 2, 2, 1, 3, 4, 4, 5, 6]
5
Output: 3
```

**Example2**

```
Input: 
[1, 2, 2, 1, 3, 4, 4, 5, 6]
7
Output: -1
```

**Example3**

```
Input: 
[1, 2, 2, 1, 3, 4]
3
Output: 3
```





# 思路

查找在从0到给定的terminating number这段subarray中的第一个unique number

### **想法一**

开一个HashMap<int, boolean>, 用O(n)先遍历一边数组并且计boolean, 然后第二次遍历的时候找到第一个boolean = True的,

**<font color = red>但是这样是不行的, data stream的特点是只能遍历一次, 所以需要inplace的查找!</font>**

### 方法二

使用Hash Map和 Linked List来处理,有点难理解

+ 因为要做到仅遍历一遍, 所以在遇到重复的value时就要做到能够remove这个值. 而且在遍历完后,要能确保可以从head直接到first unique, 所以必须得用上LinkedList
+ 但是又因为每次遇到value都要检测存在不存在, 所以并不能用ArrayList< ListNode > 然后方便的删除

**<font color = red>尝试用HashMap + 带有duplicate 属性的node来做</font>**

+ 首先需要做到每次遍历到的值都以node接在上一个值的后面, 因此需要定义一个**tail**来保持更新, 并且**将当前的number和node 放入hashmap**

  ```java
  tail.next = node;
  tail = tail.next;
  map.put(number, node);
  ```

  因此这个tail必须得是全局的,为了避免全局变量的出现, 用一个**DataStream的class来设置全局的tai**l(当然还需要一个head作为dummy node)

+ 每次call ds.add(nums[i])时, 首先检测下是否存在于map里, 是的话就将其unique属性修改为false, (默认为true)

+ 当主循环遇到terminating number时, 用一个firstunique函数, 从head开始遍历, 直到找到**第一个unique属性为true的node, 返回其value**, 否则返回-1

**注意: 在contructor里就要把head的unique属性设置为false,以防查找的时候直接返回head**

总时间复杂度为O(n), 空间复杂度为O(n)



# 题解

时间复杂度为O(n), 空间复杂度为O(n)

```java
class UniqueNode {
    private int val;
    private UniqueNode next;
    private UniqueNode prev;
    private Boolean unique;
    public UniqueNode(int val) {
        this.val = val;
        this.next = null;
        this.unique = true;
    }
}

class DataStream {
    private UniqueNode head;
    private UniqueNode tail;
    // 每个int指向datastream中对应的node
    private Map<Integer, UniqueNode> map;
    private Set<Integer> duplicates;

    public DataStream() {
        head = new UniqueNode(0);
        head.unique = false;
        tail = head;
        map = new HashMap<Integer, UniqueNode>();
    }

    private void add(int number) {
        if (map.containsKey(number)) {
            map.get(number).unique = false;
        } else {
            UniqueNode node = new UniqueNode(number);
            tail.next = node; 

            map.put(number, node);
            tail = tail.next;
        }
    }

    private int firstUnique() {
        while (head != null) {
            if (head.unique) {
                return head.val;
            } else {
                head = head.next;
            }
        }
        return -1;
    }
}
```

```java
public int firstUniqueNumber(int[] nums, int number) {
    // Write your code here
    DataStream ds = new DataStream();
    for (int i = 0; i < nums.length; i++) {
        ds.add(nums[i]);
        if (nums[i] == number) {
            return ds.firstUnique();
        }
    }
    return -1;
}
```





