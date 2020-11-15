# 题目地址

https://www.lintcode.com/problem/implement-queue-by-linked-list



# 题目描述

Implement a Queue by linked list. Support the following basic methods:

1. `enqueue(item)`. Put a new item in the queue.
2. `dequeue()`. Move the first item out of the queue, return it.

### Example

Example 1:

```
Input:
enqueue(1)
enqueue(2)
enqueue(3)
dequeue() // return 1
enqueue(4)
dequeue() // return 2
```

Example 2:

```j a vj a
Input:
enqueue(10)
dequeue()// return 10
```



# 思路

### 方法一

用链表实现

首先要定义Node class, 主要是next和val两个属性

+ enqueue

  + 注意当queue本身是空的时候,要使得head和tail指向同一个值

  + queue本身不空时,直接对tail进行操作即可

+ dequeue

  + 当queue不止一个时,head后移即可
  + 当queue只有一个时,head和tail都指向null
  + 当queue空,直接return

### 方法二

用数组实现——循环队列

要保持队列的最大长度是size, **数组一直是那个数组但是head和tail在改变而已**

<font color = blue>规定循环队列只剩一个空位就是队列已满</font>

head和tail最初都是0

+ enqueue
  	+ 每次放入都要queue[tail++] = item, 确保tail + 1
  	+ <font color = red>接着需要求 tail = tail % size, 保证tail的跟进,比如head = 1, tail = 9时,放入一个元素,tail = 10, 然后tail = 0, 即下一次在放入值就要从0开始放了</font>
  	+ 每次enqueue开始时先检测 (tail + 1) % size == head, size = 10的数组最多放9个

+ dequeue
  + 如果队列空了,head = tail, 直接返回-1
  + 如果不空, 依旧是取queue[head++], 但是<font color = red>head = head % size 来更新head</font>



# 题解

### 方法一

先声明一个Node class

```java
class Node {
    public Node next;
    public int val;
    public Node(int val) {
        this.val = val;
        this.next = null;
    }
}
```

再实现功能

```java
public class MyQueue {
	// 加上属性
    public Node head, tail;

    // 初始化head和tail都是null
    public MyQueue() {
        this.head = null;
        this.tail = null;
    }

    public void enqueue(int item) {
        // edge case: 空队列
        if (head == null) {
            head = new Node(item);
            tail = head;
        } else {
            tail.next = new Node(item);
            tail = tail.next;
        }

    }

    public int dequeue() {
        // edge case: 空队列
        if (head == null) {
            return -1;
        } 
        int ret = head.val;
        head = head.next;
        // edge case: 只有一个元素
        if (head == null) {
            tail = head;
        }
        return ret;
    }
}
```

### 方法二

```java 
public class MyQueue {
    public int head, tail;
    public int size = 10; // size看需求取
    public int[] queue = new int[size];
    
    public MyQueue() {
        head = tail = 0;
    }
    
    public void enqueue(int item) {
        if ((tail + 1) % size == head) {
            return;
        }
        queue[tail++] = item;
        tail %= size;
    }
    
    public int dequeue(){
        if (head == tail) {
            return -1;
        }
        int ret = queue[head++];
        head %= size;
        return ret;
    }
}
```

