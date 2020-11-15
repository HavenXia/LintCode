# 题目地址

https://www.lintcode.com/problem/course-schedule-ii



# 题目描述

There are a total of n courses you have to take, labeled from `0` to `n - 1`.
Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: `[0,1]`

Given the total number of courses and a list of prerequisite pairs, return the ordering of courses you should take to finish all courses.

There may be multiple correct orders, you just need to return one of them. If it is impossible to finish all courses, return an empty array.

### Example

**Example 1:**

```
Input: n = 2, prerequisites = [[1,0]] 
Output: [0,1]
```

**Example 2:**

```
Input: n = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]] 
Output: [0,1,2,3] or [0,2,1,3]
```





# 思路

依旧是按照拓扑排序的思路

+ 首先统计入度, 用一个HashMap <Integer, List<Integer>>(<font color = blue>第一个是courseNum, 第二个是对应的后续节点</font>),并且用int[]统计入度, 因为courseNum都可以正好对应index
+ 声明一个queue, 遍历prerequisites, 把入度为0的course加入queue
+ BFS,每取出一个入度为0的course, 就在result中加入当前node, 并把所有它的**后续节点**的入度-1
+ 当入度为0, 则将该点加入queue
+ 最后出循环检测下Numchoose和最初的总课程数是否相等





# 题解

时间复杂度O(n + m), 每个点都poll一次,每条边都在减入度时遍历了一次

空间复杂度O(n+m), map的key最多有n个, 所开辟的arrayList空间总和最多是m

```java
public int[] findOrder(int numCourses, int[][] prerequisites) {
    // 创建map, 前为course号码,后为其后续节点
    Map<Integer, List<Integer>> map = new HashMap<Integer, List<Integer>>();
    int[] indegree = new int[numCourses];

    // 初始化节点
    // 只初始化有后续节点的节点
    for (int[] pair : prerequisites) {
        map.put(pair[1], new ArrayList<Integer>());
    }

    // 遍历存入后续节点以及统计入度
    for (int[] pair : prerequisites) {
        map.get(pair[1]).add(pair[0]);
        indegree[pair[0]]++;
    }

    int numChoose = 0;
    Queue<Integer> queue = new LinkedList<Integer>();
    int[] result = new int[numCourses];

    // 入度为0加入队列
    // 这里的index正是courseNum
    for (int i = 0; i < indegree.length; i++) {
        if (indegree[i] == 0) {
            queue.offer(i);
        }
    }

    while (!queue.isEmpty()) {
        int nextCourse = queue.poll();
        result[numChoose] = nextCourse;
        numChoose++;

        // 把每个节点的后续节点的入度-1
        // 如果某个点入度为0,后续也没有,因为已经进入了result, 可以直接continue
        if (!map.containsKey(nextCourse)) {
            continue;
        }
        for (int next : map.get(nextCourse)) {
            indegree[next]--;
            if (indegree[next] == 0) {
                queue.offer(next);
            }
        }
    }
	// 存在拓扑序则相等
    if (numChoose == numCourses) {
        return result;
    }
    return new int[0];
}
```

