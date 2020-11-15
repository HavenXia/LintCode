# 题目地址

https://www.lintcode.com/problem/sequence-reconstruction



# 题目描述

Check whether the original sequence `org` can be uniquely reconstructed from the sequences in `seqs`. The org sequence is a permutation of the integers from 1 to n, with 1≤*n*≤10<sup>4</sup>. Reconstruction means building a shortest common supersequence of the sequences in `seqs` (i.e., a shortest sequence so that all sequences in `seqs` are subsequences of it). Determine whether there is only one sequence that can be reconstructed from `seqs` and it is the `org` sequence.

### Example

Example 1:

```
Input:org = [1,2,3], seqs = [[1,2],[1,3]]
Output: false
Explanation:
[1,2,3] is not the only one sequence that can be reconstructed, because [1,3,2] is also a valid sequence that can be reconstructed.
```

Example 2:

```
Input: org = [1,2,3], seqs = [[1,2]]
Output: false
Explanation:
The reconstructed sequence can only be [1,2].
```

Example 3:

```
Input: org = [1,2,3], seqs = [[1,2],[1,3],[2,3]]
Output: true
Explanation:
The sequences [1,2], [1,3], and [2,3] can uniquely reconstruct the original sequence [1,2,3].
```

Example 4:

```
Input:org = [4,1,5,2,6,3], seqs = [[5,2,6,3],[4,1,5,2]]
Output:true
```





# 思路

本题要求的是,检测 `arg` 是否是包含所有 `seqs` 中序列的最短序列

本题中org的序列是从1到n的, 意思就是n = org.length

<font color = blue>某个序列的子序列是从最初序列通过去除某些元素但不破坏余下元素的相对位置（在前或在后）而形成的新序列</font>

因此,本题实质上依旧是topo sorting, seqs中的数组代表了已经给定的拓扑序

+ 遍历所有sequence, HashMap<Integer, Set<Integer>>, 前一个是num,后一个是其后置节点, <font color = blue>还需要一个HashMap来存储Num和其入度</font>.如果遇到相同的点, 需要先检测是否Num对应的后续节点已经有了, 没有的话则加入并且后续节点入度+1(<font color = red>因为此题里的前后关系可能出现重复,所以需要检测,一般的时候是没有的</font>)
+ 声明一个queue, 把入度为0的点加入, 开始BFS, <font color = red>此时while的条件是(q.size() == 1), 因为要保证唯一性, queue中一定只能存在一个元素</font>
+ 每次poll之后,立刻检测下result[i] 是否等于org[i], 一旦出现不同直接给false
+ 每取出一个入度为0的Num, 就在result中加入当前Num, 并把所有它的**后续节点**的入度-1
+ 最后比较下得到序列的长度是否相等, 否就return false

<font color = red>**本题有相当多的corner case,需小心对待**</font>







# 题解

时间复杂度O(n + m), 每个点都poll一次,每条边都在减入度时遍历了一次

空间复杂度O(n + m), map的key最多有n个, 所开辟的arrayList空间总和最多是m

```java
public boolean sequenceReconstruction(int[] org, int[][] seqs) {
    // null case
    if (seqs == null || seqs.length == 0) {
        return false;
    }

    // 声明包含后续数字的hashmap和入度的HashMap
    Map<Integer, Set<Integer>> map = new HashMap<Integer, Set<Integer>>();
    Map<Integer, Integer> indegree = new HashMap<Integer, Integer>();

    // 初始化map中的ArrayList
    for (int num : org) {
        map.put(num, new HashSet<Integer>());
        indegree.put(num, 0);
    }

    // 遍历sequence, 完成两个hashMap的建立
    // cnt是为了确保seqs中总数字个数大于org的个数, 避免[1],[[],[]]这样的组合
    int cnt = 0;
    for (int[] seq : seqs) {

        cnt += seq.length;
        // seq是可能为[]的,因此要单独continue
        if (seq.length == 0) {
            continue;
        }

        // 确保seq中没有数字不出现在org中
        for (int i = 0; i < seq.length; i++) {
            // 如果有任何一个数没有出现在org里,就直接return false
            if (!map.containsKey(seq[i])) {
                return false;
            }
        }

        // 遍历seq, 完成两个hashMap的建立
        for (int i = 0; i < seq.length - 1; i++) {
            // 必须是没有加入过才能这么做
            if (!map.get(seq[i]).contains(seq[i + 1])) {
                map.get(seq[i]).add(seq[i + 1]);
                indegree.put(seq[i + 1], indegree.get(seq[i + 1]) + 1);
            }
        }
    }

    // cnt是为了确保seqs中总数字个数大于org的个数
    if (cnt < org.length) {
        return false;
    }

    // 声明队列, 并加入入度为0的点
    Queue<Integer> queue = new LinkedList<Integer>();
    for (int number : indegree.keySet()) {
        if (indegree.get(number) == 0) {
            queue.offer(number);
        }
    }

    // 开始BFS, 唯一拓扑序意味着queue中必须只能有一个元素
    // 如果任何时候有两个元素,那么先后顺序就可以被改变

    // if (queue.size() != 1) {
    //     return false;
    // }

    int count = 0;
    while (queue.size() == 1) {
        int nowNum = queue.poll();
        // 检测是否对应org相等
        if (nowNum != org[count]) {
            return false;
        }
        count++;
        // 遍历消去后续数字的入度
        for (int next : map.get(nowNum)) {
            indegree.put(next, indegree.get(next) - 1);
            if (indegree.get(next) == 0) {
                queue.offer(next);
            }
        }
    }

    return count == org.length;
}
```

