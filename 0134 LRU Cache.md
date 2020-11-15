# 题目地址

https://www.lintcode.com/problem/lru-cache



# 题目描述

Design and implement a data structure for Least Recently Used (LRU) cache. It should support the following operations: `get` and `set`.

- `get(key)` Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
- `set(key, value)` Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.

Finally, you need to return the data from each get.

### Example

**Example1**

```
Input:
LRUCache(2)
set(2, 1)
set(1, 1)
get(2)
set(4, 1)
get(1)
get(2)
Output: [1,-1,1]
Explanation：
cache cap is 2，set(2,1)，set(1, 1)，get(2) and return 1，set(4,1) and delete (1,1)，because （1,1）is the least use，get(1) and return -1，get(2) and return 1.
```

**Example 2:**

```
Input：
LRUCache(1)
set(2, 1)
get(2)
set(3, 2)
get(2)
get(3)
Output：[1,-1,2]
Explanation：
cache cap is 1，set(2,1)，get(2) and return 1，set(3,2) and delete (2,1)，get(2) and return -1，get(3) and return 2.
```

Input test data (one parameter per line)How to understand a testcase?



# 思路

**Clarification:** Least recently used 不代表least used的!! 这不是**最少使用**是,而是**最久没有使用**的!



### 方法一

使用comparator来做, 用一个Priority Queue 来保存 int [2]  来保存value和counter

同时需要一个HashSet维持get function

<font color = red>但是问题在于Priority Queue不能remove 对应的int[], 因为这是objective</font> , 所以无法用Priority Queue



### 方法二

用LinkedList来做,自定义Listnode

类似[First Unique Number in Data Stream](/Users/parallax/Documents/求职/LintCode/0685 First Unique Number in Data Stream .md), 每个value对应的值都是listnode里

**get(int key)**

+ 如果HashMap已经存在key了, 就return -1
+ 否则, 先用map获取prev
  + 首先检测下当前tail 是不是就是当前的key
    + 如果是就不用移动到最后一位了
    + 否则prev.next = prev.next.next (<font color = green>原位置去除</font>), 然后把tail.next = cur, 更新tail, **相当于放到LinkedList最后**
    + 然后要更新map里key对应的prev

**set(int key, int value)**

+ 首先检测是不是在map里了, 是的话就和get一样移动到最后, 然后更新一下它的value(<font color = red>可以把移动到tail 包装成一个函数</font>)
+ 如果不在map里, 直接在tail后接上然后放到map里, 并且更新tail, **让count++**

当count等于capcacity的时候, 这时候设置的dummy node就起到作用, 把head.next 移出map, 然后head.next = head.next 最后在末尾加上新node 即可

# 题解

```java
public class LRUCache {
    class ListNode {
        public int key, val;
        public ListNode next;

        public ListNode(int key, int val) {
            this.key = key;
            this.val = val;
            this.next = null;
        }
    }
    private int capacity, size;
    private ListNode dummy, tail;
    private Map<Integer, ListNode> keyToPrev;

    /*
    * @param capacity: An integer
    */
    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.keyToPrev = new HashMap<Integer, ListNode>();
        this.dummy = new ListNode(0, 0);
        this.tail = this.dummy;
    }

    private void moveToTail(int key) {
        ListNode prev = keyToPrev.get(key);
        ListNode curt = prev.next;

        if (tail == curt) {
            return;
        }

        prev.next = prev.next.next;
        tail.next = curt;
        curt.next = null;

        keyToPrev.put(prev.next.key, prev);

        keyToPrev.put(curt.key, tail);

        tail = curt;
    }

    /*
     * @param key: An integer
     * @return: An integer
     */
    public int get(int key) {
        if (!keyToPrev.containsKey(key)) {
            return -1;
        }

        moveToTail(key);

        // the key has been moved to the end
        return tail.val;
    }

    /*
     * @param key: An integer
     * @param value: An integer
     * @return: nothing
     */
    public void set(int key, int value) {
        // get method will move the key to the end of the linked list
        if (get(key) != -1) {
            ListNode prev = keyToPrev.get(key);
            prev.next.val = value;
            return;
        }

        if (size < capacity) {
            size++;
            ListNode curt = new ListNode(key, value);
            tail.next = curt;
            keyToPrev.put(key, tail);

            tail = curt;
            return;
        }

        // replace the first node with new key, value
        ListNode first = dummy.next;
        keyToPrev.remove(first.key);

        first.key = key;
        first.val = value;
        keyToPrev.put(key, dummy);

        moveToTail(key);
    }
}
```

