# 题目地址

https://www.lintcode.com/problem/middle-of-linked-list



# 题目描述

Find the middle node of a linked list.

### Example

**Example 1:**

```
Input:  1->2->3
Output: 2	
Explanation: return the value of the middle node.
```

**Example 2:**

```
Input:  1->2
Output: 1	
Explanation: If the length of list is  even return the value of center left one.	
```

### Challenge

If the linked list is in a data stream, can you find the middle without iterating the linked list again?



# 思路

### 暴力遍历

首先暴力遍历一遍所有的点,得到linked list长度

由于此处even时需要返回的是 <font color = blue>left middle</font>,所以需要分开对待

+  如果length是odd, 就循环 `head.next`  len/2 次
+  如果length是even, 就循环 `head.next`  len/2 - 1 次

时间复杂度自然是O(n),空间复杂度O(1)



### 快慢指针

这是实现challenge的方法, 虽然O(n/2) 和O(n) 一个量级,但是确实是更快了

但是注意: 由于even返回的是 <font color = blue>left middle</font>, 所以在实现的时候不能每次fast走两步

都要检测下是否fast已经是null了

**注意: while (fast != null & fast.next != null) 一定不能漏掉fast.next, 因为null.next是不存在的,会导致Nullpointer**

+ 如果是的话,直接返回slow
+ 如果不是,再接着检测下是否fast.next是null, 如果是的话就返回slow.next
+ <font color = red>如果fast.next 还是存在, 那就让fast走两步,进入下一轮循环</font> 

### 

# 题解

### 暴力遍历

时间复杂度O(n),空间复杂度O(1)

```java
public ListNode middleNode(ListNode head) {
    // write your code here
    if (head == null) {
        return null;
    }

    ListNode cur = head;
    int length = 0;

    while (cur != null) {
        cur = cur.next;
        length++;
    }

    if (length % 2 != 0) {
        // 如果是奇数,走length/2步
        for (int i = 0; i < length / 2; i++) {
            head = head.next;
        }
    } else {
        // 如果是偶数,只要走 length/2 - 1 步
        for (int i = 0; i < length / 2 - 1; i++) {
            head = head.next;
        }
    }

    return head;
}
```



### 快慢指针

时间复杂度O(n/2),空间复杂度O(1)

```java
public ListNode middleNode(ListNode head) {
    // write your code here
    if (head == null) {
        return null;
    }

    ListNode fast = head;
    ListNode slow = head;

    while (fast != null && fast.next != null) {
        // 快指针一次两步
        fast = fast.next.next;

        if (fast == null) {
            return slow;
        }

        // 然后慢指针一次一步
        slow = slow.next;
    }

    return slow;
}
```

