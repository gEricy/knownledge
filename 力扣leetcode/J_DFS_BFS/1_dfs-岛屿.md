

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
    def numIslands(self, grid):
        m, n = len(grid), len(grid[0])
        def dfs(i, j):
            if i >= m or j >= n:   # 越界
                return
            if grid[i][j] != '1':  # 当前元素不是岛屿
                return
            grid[i][j] = '0'  # 访问当前位置后，就将其标为0，防止之后再次访问
            for di, dj in [[0, 1], [0, -1], [1, 0], [-1, 0]]:
                next_i, next_j = i + di, j + dj
                dfs(next_i, next_j)

        ans = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == '1':
                    ans += 1
                    dfs(i, j)
        return ans
```

#### [695. 岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/)

```python
class Solution(object):
    def maxAreaOfIsland(self, grid):
        m, n = len(grid), len(grid[0])
        def dfs(i, j):
            if i >= m or j >= n:  # 越界
                return 0
            if grid[i][j] != 1:  # 当前元素不是岛屿
                return 0
            grid[i][j] = 0
            area = 1  # 初始值为1
            for di, dj in [[0, 1], [0, -1], [1, 0], [-1, 0]]:
                next_i, next_j = i + di, j + dj
                area += dfs(next_i, next_j)
            return area
        ans = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == 1:
                    ans = max(ans, dfs(i, j))
        return ans
```



