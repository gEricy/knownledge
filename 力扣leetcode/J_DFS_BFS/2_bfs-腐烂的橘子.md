### BFS 广度优先遍历

- queue



---



#### [994. 腐烂的橘子](https://leetcode-cn.com/problems/rotting-oranges/)

- queue中存放的元素: (i, j, time)

```python
class Solution:
    def in_area(self, i, j):
        return i>=0 and i<self.m and j>=0 and j<self.n
    
    def orangesRotting(self, grid):
        self.m, self.n = len(grid), len(grid[0])
        time = 0
        queue = []
        
        # 初始化: 添加所有腐烂的橘子到queue
        for i in range(self.m):
            for j in range(self.n):
                if grid[i][j] == 2:
                    queue.append((i, j, time))
        # bfs
        while queue:
            i, j, time = queue.pop(0)
            for di, dj in [(1, 0), (-1, 0), (0, 1), (0, -1)]:
                next_i, next_j = i+di, j+dj
                if self.in_area(next_i, next_j) and grid[next_i][next_j] == 1:
                    grid[next_i][next_j] = 2
                    queue.append((next_i, next_j, time + 1))
                    
        # if there are still fresh oranges, return -1
        for self.m in grid:
            if 1 in self.m: 
                return -1

        return time
```

