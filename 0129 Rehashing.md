# 题目地址

https://www.lintcode.com/problem/rehashing

# 题目描述

The size of the hash table is not determinate at the very beginning. If the total size of keys is too large (e.g. size >= capacity / 10), we should double the size of the hash table and rehash every keys. Say you have a hash table looks like below:

```
size=3`, `capacity=4
[null, 21, 14, null]
       ↓    ↓
       9   null
       ↓
      null
```

The hash function is:

```
int hashcode(int key, int capacity) {
    return key % capacity;
}
```

here we have three numbers, 9, 14 and 21, where 21 and 9 share the same position as they all have the same hashcode 1 (21 % 4 = 9 % 4 = 1). We store them in the hash table by linked list.

rehashing this hash table, double the capacity, you will get:

```
size=3`, `capacity=8
index:   0    1    2    3     4    5    6   7
hash : [null, 9, null, null, null, 21, 14, null]
```

Given the original hash table, return the new hash table after rehashing .

### Example

***Example 1***

```
Input : [null, 21->9->null, 14->null, null]
Output : [null, 9->null, null, null, null, 21->null, 14->null, null]
```

### Notice

For negative integer in hash table, the position can be calculated as follow:

- **C++/Java**: if you directly calculate -4 % 3 you will get -1. You can use function: a % b = (a % b + b) % b to make it is a non negative integer.
- **Python**: you can directly use -1 % 3, you will get 2 automatically.

Input test data (one parameter per line)How to understand a testcase?

# 思路

当size >= capacity / 10, **存储的元素超过容量的十分之一**, 需要rehashing

double Hash Size的时候, HASH_SIZE 变化了, 因此每个value的位置都变了

比如ex里hash size 从4 变成8, 所以9 要在index 1的位置了

因此需要对每个linkedlist进行遍历存放, 遍历时每次节点放在头部

在负数时候需要使用 `(head.val % hashSize + hashSize) % hashSize` 来获取**非负Hash Code**, <font color = red>此处可以直接不论正负都用这个</font>



# 题解

时间复杂度O(n), 空间复杂度O(2 * oldHashSize)

```java
public ListNode[] rehashing(ListNode[] hashTable) {
    // write your code here
    int hashSize = hashTable.length * 2;
    ListNode[] result = new ListNode[hashSize];

    for (ListNode head : hashTable) {
        while (head != null) {
            int newIndex = (head.val % hashSize + hashSize) % hashSize;
            head.next = result[newIndex];
            result[newIndex] = head;
        }
    }
    return result;
}
```

