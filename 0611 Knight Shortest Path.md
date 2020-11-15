# 题目地址

https://www.lintcode.com/problem/knight-shortest-path/



## 题目描述

Given a knight in a chessboard (a binary matrix with `0` as empty and `1` as barrier) with a `source` position, find the shortest path to a `destination` position, return the length of the route.
Return `-1` if destination cannot be reached.

### Example

**Example 1:**

```
Input:
[[0,0,0],
 [0,0,0],
 [0,0,0]]
source = [2, 0] destination = [2, 2] 
Output: 2
Explanation:
[2,0]->[0,1]->[2,2]
```

**Example 2:**

```
Input:
[[0,1,0],
 [0,0,1],
 [0,0,0]]
source = [2, 0] destination = [2, 2] 
Output:-1
```

### Clarification

If the knight is at (*x*, *y*), he can get to the following positions in one step:

```
(x + 1, y + 2)
(x + 1, y - 2)
(x - 1, y + 2)
(x - 1, y - 2)
(x + 2, y + 1)
(x + 2, y - 1)
(x - 2, y + 1)
(x - 2, y - 1)
```

### Notice

source and destination must be empty.
Knight can not enter the barrier.
Path length refers to the number of steps the knight takes.



# 思路

本题是[Numbers of Islands](/Users/parallax/Documents/CS/LintCode/0433 Number of Islands.md)的变体,增加了移动的方向,整体依旧是按照BFS的思想来做

声明一个queue和map,<font color = blue>此处的map和BFS模板里的distnace类似但并不相同, 用来存储**走过的节点**和**Step**</font>

在矩阵中,可以不使用Point class就表示节点, <font color = red>因为在矩阵中每个点离(0,0)的距离都是不同的, 因此可以用当前point.x * grid[0].length + point.y 来表示位置</font>

+ 在queue和map中放入source点, map.put(source,0), 此处0是指0 step
+ 首先套一个while !isEmpty(),设置返回条件:<font color = blue>当前point的x和y都等于destination</font>
+ 对八个方向进行遍历, 如果出界就continue,<font color = red>如果撞到1就continue</font>,<font color = blue>如果之前走到过就也continue</font>,因为要求的是最短距离
+ 如果都满足,就把这个nextpoint放到map里,并且value是当前map.get(point) + 1, <font color = red>意思是step + 1</font>,并且把点放入queue中

**<font color = blue>注意! 本题中不需要分层,因为即使不分层,最短step也是通过一个点到下一个点来确定的</font> **



<font color = red>**不能在map里存储Point Class**, 每次对于当前的节点创造出的next 是每次都不一样的! </font>

不能像模板的node一样检测是否node已经在map中了!!!! 因为new出来的next就算属性和map里的完全相同, 也不行!!

**只能用distance的距离!!!**



**方法二**

不用HashMap,直接分层遍历

每次在分层结束后step++

<font color = red>注意! 一定要每次用int size进行分层,直接用 queue.size会一直随着运行而变化!!!</font>





# 题解

时间复杂度O(RxC), 最坏情况下全部走一遍

空间复杂度O(RxC),所有点都放一遍

```java
public int shortestPath(boolean[][] grid, Point source, Point destination) {
    // null case
    if (grid == null || grid[0] == null || grid.length == 0 || grid[0].length == 0) {
        return -1;
    }
	
    Queue<Point> queue = new LinkedList<Point>();
    Map<Integer, Integer> map = new HashMap<Integer, Integer>();
    // 八个方向的2d数组
    int[][] direction = {{1, 2}, {1, -2}, {-1, 2}, {-1, -2}, {2, 1}, {2, -1}, {-2, 1}, {-2, -1}};

    queue.offer(source);
    // 放入离(0,0)的距离值和step数
    map.put(source.x * grid[0].length + source.y, 0);

    while (!queue.isEmpty()) {
        Point point = queue.poll();
        // 返回条件
        if (point.x == destination.x && point.y == destination.y) {
            return map.get(point.x * grid[0].length + point.y);
        }

        for (int i = 0; i < 8; i++) {

            Point next = new Point(point.x + direction[i][0], point.y + direction[i][1]);
			// 出界
            if (next.x < 0 || next.x >= grid.length || next.y < 0 || next.y >= grid[0].length) {
                continue;
            }
            // 撞墙
            if (grid[next.x][next.y]) {
                continue;
            }
			// 已经走过
            if (map.containsKey(next.x * grid[0].length + next.y)) {
                continue;
            }
			// step+1
            map.put(next.x * grid[0].length + next.y,map.get(point.x * grid[0].length + point.y) + 1);
            queue.offer(next);
        }
    }
    return -1;
}
```



### 方法二

```java
public int shortestPath(boolean[][] grid, Point source, Point destination) {
    // write your code here
    if (grid == null || grid[0] == null || grid.length == 0 || grid[0].length == 0) {
        return -1;
    }

    int[][] direction = {{1,2},{1,-2},{-1,2},{-1,-2},{2,1},{2,-1},{-2,1},{-2,-1}};
    Queue<Point> queue = new LinkedList<Point>();
    int step = 0;
    queue.offer(source);
    while (!queue.isEmpty()) {

        int size = queue.size();
        // 分层
        for (int k = 0; k < size; k++) {
            Point point = queue.poll();

            if (point.x == destination.x && point.y == destination.y) {
                return step;
            }

            for (int i = 0; i < 8; i++) {
                Point next = new Point(point.x + direction[i][0], point.y + direction[i][1]);

                if (next.x < 0 || next.x >= grid.length || next.y < 0 || next.y >= grid[0].length) {
                    continue;
                }
                if (grid[next.x][next.y] == true) {
                    continue;
                }

                grid[next.x][next.y] = true;
                queue.offer(next);

            }
        }
        step++;
    }
	return -1;
}
```

