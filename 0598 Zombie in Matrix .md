# 题目地址

https://www.lintcode.com/problem/zombie-in-matrix



# 题目描述

Given a 2D grid, each cell is either a wall `2`, a zombie `1` or people `0` (the number zero, one, two).Zombies can turn the nearest people(up/down/left/right) into zombies every day, but can not through wall. How long will it take to turn all people into zombies? Return `-1` if can not turn all people into zombies.

### Example

Example 1:

```
Input:
[[0,1,2,0,0],
 [1,0,0,2,1],
 [0,1,0,0,0]]
Output:
2
```

Example 2:

```
Input:
[[0,0,0],
 [0,0,0],
 [0,0,1]]
Output:
4
```





# 思路

类似于[Knight Shortest Path](/Users/parallax/Documents/CS/LintCode/0611 Knight Shortest Path.md)

+ 首先把给定的grid复制到visited里面去(是否复制看面试官想法), 在循环的时候把代表zombie的点加入到queue中
+ 按照BFS的模板, 对每个点的上下左右进行一遍检测, <font color = red>但这次是分层遍历,因为是按照每一天来的</font>

<font color = red>**分层遍历一定要用 `int size = queue.size()` 来遍历,绝对不能用 `queue.size()`, 因为size会随着BFS变化!!!**</font>





# 题解

时间复杂度O(R*C), <font color = blue>来自创建visited和BFS</font>

空间复杂度O(R*C), 建立visited数组用掉

```java
class Point {
    int x;
    int y;
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

```java
public int zombie(int[][] grid) {
    // null case
    if (grid == null || grid.length == 0 || grid[0] == null || grid.length == 0) {
        return -1;
    }

    // 声明queue和visited数组
    Queue<Point> queue = new LinkedList<Point>();
    int[][] visited = new int[grid.length][grid[0].length];
    int[][] direction = {{1, 0}, {0, 1}, {-1, 0}, {0, -1}};
    int people = 0;

    // 遍历加入queue以及复制grid
    for (int i = 0; i < grid.length; i++) {
        for (int j = 0; j < grid[0].length; j++) {
            visited[i][j] = grid[i][j];
            // 如果是zombie就加入queue
            if (grid[i][j] == 1) {
                queue.offer(new Point(i, j));
            }
            // 如果是people就+1
            if (grid[i][j] == 0) {
                people++;
            }
        }
    }

    // BFS
    int time = 0;
    while (!queue.isEmpty()) {
        time++;
        // 一定要分层遍历, 一定要用size不能用queue.size()否则会一直不出循环
        int size = queue.size();
        for (int k = 0; k < size; k++) {

            Point nowPoint = queue.poll();
            for (int i = 0; i < 4; i++) {
                Point next = new Point(nowPoint.x + direction[i][0], nowPoint.y + direction[i][1]);
                // 出界
                if (next.x < 0 || next.x >= grid.length || next.y < 0 || next.y >= grid[0].length) {
                    continue;
                }
                // 撞墙
                if (visited[next.x][next.y] == 2) {
                    continue;
                }
                // 感染人类并加入queue, people--
                if (visited[next.x][next.y] == 0) {
                    visited[next.x][next.y] = 1;
                    queue.offer(next);
                    people--;
                }

                if (people == 0) {
                    return time;
                }
            }
        }
    }
    return -1;
}
```

