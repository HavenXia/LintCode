# 题目地址

https://www.lintcode.com/problem/heapify

# 题目描述

Given an integer array, heapify it into a min-heap array.

For a heap array A, A[0] is the root of heap, and for each A[i], A[i * 2 + 1] is the left child of A[i] and A[i * 2 + 2] is the right child of A[i].

### Example

***Example 1***

```
Input : [3,2,1,4,5]
Output : [1,2,3,4,5]
Explanation : return any one of the legitimate heap arrays
```

### Challenge

O(n) time complexity

### Clarification

***What is heap?*** ***What is heapify?*** ***What if there is a lot of solutions?***

- Heap is a data structure, which usually have three methods: push, pop and top. where "push" add a new element the heap, "pop" delete the minimum/maximum element in the heap, "top" return the minimum/maximum element.
- Convert an unordered integer array into a heap array. If it is min-heap, for each element A[i], we will get A[i * 2 + 1] >= A[i] and A[i * 2 + 2] >= A[i].
- Return any of them.



# 思路

此处需要的是Min Heap, 所以需要对array的元素进行siftup和siftdown

+ 基于sift up思想

  ```java
  private void siftup(int[] A, int k) {
      while (k != 0) {
          int father = (k - 1) / 2;
          if (A[k] > A[father]) {
              break;
          }
          int temp = A[k];
          A[k] = A[father];
          A[father] = temp;
  
          k = father;
      }
  }
  
  public void heapify(int[] A) {
      for (int i = 0; i < A.length; i++) {
          siftup(A, i);
      }
  }
  ```

  对于每个element都从当前开始,一直和father node进行比较, 比如对于[3,2,1,4,5]

  <font color = green>[3,2,1,4,5] - [2,3,1,4,5] - [1,3,2,4,5] - [1,3,2,4,5] - [1,3,2,4,5]</font>, 由于是从前往后走, 所以每个当前node只要和上一个比较就够了

  **为什么要用while循环?** 尽管前面可能排序好了.[3,4,5,7,1] 走到1的时候还是要一直往前换,而不是只让1和5对调

  时间复杂度O(nlogn), <font color = red>n来自每一次的heapify, logn 来自从当前一直交换父节点,最多logn次</font>

  **注意:** 最后一层约为n/2个node, 每个node都最多logn, 所以nlogn没跑

  

+ 基于siftdown思想

  ```java
  private void siftdown(int[] A, int k) {
      while (k * 2 + 1 < A.length) {
          int son = k * 2 + 1;   // A[i] 的左儿子下标。
          if (k * 2 + 2 < A.length && A[son] > A[k * 2 + 2])
              son = k * 2 + 2;     // 选择两个儿子中较小的。
          if (A[son] >= A[k])      
              break;
  
          int temp = A[son];
          A[son] = A[k];
          A[k] = temp;
          k = son;
      }
  }
  
  public void heapify(int[] A) {
      for (int i = (A.length - 1) / 2; i >= 0; i--) {
          siftdown(A, i);
      }
  }
  ```

  初始选择最接近叶子的一个父结点，与其两个儿子中较小的一个比较

  + 较小子节点 > 父节点, 满足父节点 < 两个子节点, 直接break
  + 较小子节点 <= 父节点, **交换后,** 满足父节点 < 两个子节点, <font color = red>让k作为该子节点继续往下</font>

  <font color = green>[3,2,1,4,5] - [3,2,1,4,5] - [1,2,3,4,5]</font>

  **为什么要用while循环?** 尽管后面可能排序好了.[8,1,2,3,4] 走到8的时候还是要一直往后换,**而不是只让1和8对调**

  应该是[8,1,2,3,4] - [1,8,2,3,4] - [1,3,2,8,4]即可

  **时间复杂度O(n)**

  算法从第 n/2 个数开始，倒过来进行 siftdown。也就是说，相当于从 heap 的倒数第二层开始进行 siftdown 操作.

  最后一层约为n/2个节点, 倒数第二层的节点大约有 n/4 个，这 n/4 个数，最多 siftdown 1次就到底了，所以这一层的时间复杂度耗费是 O(n/4)，

  然后倒数第三层差不多 n/8 个点，最多 siftdown 2次就到底了。所以这里的耗费是 O(n/8 * 2), 

  倒数第4层是 O(n/16 * 3)，倒数第5层是 O(n/32 * 4)

  <font color = red>T(n) = O(n/4) + O(n/8 * 2) + O(n/16 * 3) ...</font>

  <font color = red>2 * T(n) = O(n/2) + O(n/4 * 2) + O(n/8 * 3) + O(n/16 * 4) ...
  T(n) = O(n/4) + O(n/8 * 2) + O(n/16 * 3) ...</font>

  **<font color = red>2 * T(n) - T(n) = O(n/2) +O (n/4) + O(n/8) + ...
  = O(n/2 + n/4 + n/8 + ... )
  = O(n)</font>**

  **注意:**这里和siftup的区别是, siftup的最多层每个都有可能走logn的时间, 但是siftdown的最多层每个只可能走1

  



