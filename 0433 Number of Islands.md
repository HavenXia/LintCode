# 题目地址

https://www.lintcode.com/problem/number-of-islands/



# 题目描述

Given a boolean 2D matrix, `0` is represented as the sea, `1` is represented as the island. If two 1 is adjacent, we consider them in the same island. We only consider up/down/left/right adjacent.

Find the number of islands.

### Example

**Example 1:**

```
Input:
[
  [1,1,0,0,0],
  [0,1,0,0,1],
  [0,0,0,1,1],
  [0,0,0,0,0],
  [0,0,0,0,1]
]
Output:
3
```

**Example 2:**

```
Input:
[
  [1,1]
]
Output:
1
```



# 思路

<font color = blue>要先定义一个Point Class, 属性分别是x和y</font>

依旧是按照BFS, 用i和j双层for循环,遇到第一个点的时候对它周围四个点进行检测

**如何检测周围四个点?**

```java
int[][] direction = {{0,1},{0,-1},{-1,0},{1,0}}
```

然后对当前point的坐标分别加上对应的变化值

<font color = red>用一个visited来复制grid, 养成不修改原数组的好习惯</font>

如果检测到周围有point是1, 就将其变成0

<font color = blue>此时continue的条件是四周某个点出界</font>

出循环条件依旧是queue为空, 因此每次执行BFS都会把周围所有的连接点都改成false





# 题解

时间复杂度O(n*m), 来自于创建visited和遍历BFS

空间复杂度O(n*m), 来自于创建visited数组

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
public int numIslands(boolean[][] grid) {
    // null case
    if (grid == null || grid.length == 0 || grid[0].length == 0) {
        return 0;
    }
 	// 岛屿个数
    int islandNum = 0;
    // 用双层循环创建一个visited数组,方便进行修改
    // 养成不改参数的好习惯
    boolean[][] visited = new boolean[grid.length][grid[0].length];
    for (int j = 0; j < grid.length; j++) {
        for (int k = 0; k < grid[0].length; k++) {
            visited[j][k] = grid[j][k];
        }
    }
	// 双层循环, 如果某个点为true则进入BFS, 并且岛屿数量+1
    for (int i = 0; i < grid.length; i++) {
        for (int j = 0; j < grid[0].length; j++) {
            if (visited[i][j]) {
                markIsland(visited,i,j);
                islandNum++;
            }
        }
    }
    return islandNum;
}
// BFS本体, 从给定的点出发,把它和它周围所有相连接点true都改为false
private void markIsland(boolean[][] visited, int x, int y) {
	
    // 创建一个direction数组来方便走向上下左右
    int[][] direction = {{0,1},{0,-1},{-1,0},{1,0}};
    Queue<Point> queue = new LinkedList<Point>();
	
    queue.offer(new Point(x, y));
    visited[x][y] = false;
	// 出while则确保周围已经全部都被改成false
    while (!queue.isEmpty()) {
        Point point = queue.poll();
		// 四个方向,生成new point next
        for (int k = 0; k < 4; k++) {
            Point next = new Point(point.x + direction[k][0], point.y + direction[k][1]);
			// 检测是否出界,出界就continue
            if (next.x < 0 || next.x >= visited.length || next.y < 0 || next.y >= visited[0].length) {
                continue;
            }
			// 如果某一个是true, 就改为false并把next放入到queue中
            if (visited[next.x][next.y]) {
                visited[next.x][next.y] = false;
                queue.offer(next);
            }
        }
    }
}
```

