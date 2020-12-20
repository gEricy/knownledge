

## [DFS](https://leetcode-cn.com/problems/number-of-islands/solution/dao-yu-lei-wen-ti-de-tong-yong-jie-fa-dfs-bian-li-/) 深度优先遍历

下面是最简单的二叉树的前序遍历递归写法：

```C++
void dfs(TreeNode root) {
    if (root == null) { // 判断 base case
        return;
    }
    
    cout << root.val << endl;  // 访问root
    
    // 访问两个相邻结点：左子结点、右子结点
    dfs(root.left);
    dfs(root.right);
}
```

----

#### [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

```python
class Solution(object):
    def in_area(self, i, j):
        return i>=0 and i<self.m and j>=0 and j<self.n

    def dfs(self, grid, i, j):
        if self.in_area(i,j) == False:  # 越界
            return
        if grid[i][j] != "1":           # 当前元素不是岛屿
            return
        grid[i][j] = "0"  # 访问当前位置后，就将其标为0，防止之后再次访问

        for di, dj in [[0, 1], [0, -1], [1, 0], [-1, 0]]:
            next_i, next_j = i + di, j + dj
            self.dfs(grid, next_i, next_j)

    def numIslands(self, grid):
        self.m = len(grid)    # 行数
        self.n = len(grid[0]) # 列数

        ans = 0
        for i in range(self.m):
            for j in range(self.n):
                if grid[i][j] == "1":
                    ans += 1
                    self.dfs(grid, i, j)
        return ans
```

#### [695. 岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/)

```python
class Solution(object):
    def in_area(self, i, j):
        return i>=0 and i<self.m and j>=0 and j<self.n

    def dfs(self, grid, i, j):
        if self.in_area(i,j) == False:  # 越界
            return 0
        if grid[i][j] != 1:  # 当前元素不是岛屿
            return 0
        grid[i][j] = 0  # 访问当前位置后，就将其标为0，防止之后再次访问
        ans = 1
        for di, dj in [[0, 1], [0, -1], [1, 0], [-1, 0]]:
            next_i, next_j = i + di, j + dj
            ans += self.dfs(grid, next_i, next_j)
        return ans


    def maxAreaOfIsland(self, grid):
        self.m = len(grid)
        self.n = len(grid[0])

        ans = 0
        for i in range(self.m):
            for j in range(self.n):
                if grid[i][j] == 1:
                    ans = max(ans, self.dfs(grid, i, j))
        return ans
```



