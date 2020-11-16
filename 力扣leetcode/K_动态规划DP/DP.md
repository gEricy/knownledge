## 1-最简单的DP

#### [剑指 Offer 10- I. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

```python
class Solution(object):
    def fib(self, n):
        """
        :type n: int
        :rtype: int
        """
        if n == 0:
            return 0
        if n == 1:
            return 1

        dp = [0] * (n+1)  # 数组元素 [0,n]

        dp[0], dp[1] = 0, 1

        for i in range(2, n+1):
            dp[i] = dp[i-1] + dp[i-2]

        return dp[-1] % 1000000007
```



#### [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

```python
class Solution(object):
    def climbStairs(self, n):
        if n == 1:
            return 1
        if n ==2:
            return 2

        dp = [0] * (n+1)  # 数组元素 [1,n]

        dp[1], dp[2] = 1, 2

        for i in range(3, n+1):
            dp[i] = dp[i-1] + dp[i-2]

        return dp[-1]
```



---

## 2-零钱兑换

#### [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

你可以认为每种硬币的数量是无限的。

```python
class Solution(object):
    def coinChange(self, coins, amount):
        
        size = len(coins)

        NOT_FULL = (1 << 31) - 1  # 初始为NOT_FULL(INT_MAX), 表示组不成总金额

        # dp[i] 凑成总金额i所需的最少硬币个数
        dp = [NOT_FULL] * (amount+1)

        # 初始化: 组成0元, 使用0个硬币就OK
        dp[0] = 0  

        for i in range(1, amount+1):
            for coin in coins:  # 使用每一个面值的硬币
                if i-coin >= 0:
                    dp[i] = min(dp[i], dp[i-coin]+1)
    
        return -1 if dp[-1]==NOT_FULL else dp[-1]
```



---



## 3-子序列/子数组

#### [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/)
  

#### [剑指 Offer 42. 连续最大子数组和](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)

- 注意点

  - 相加后，可能更小，因此，dp函数要包括nums[i]

    ```
    dp[i] = max(dp[i-1]+nums[i], nums[i])
    ```

    

```python
class Solution(object):
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        size = len(nums)

        # dp[i] 表示以nums[i]为结尾的最大和的连续子数组
        dp = [0] * size  

        # 初始状态
        dp[0] = nums[0]

        for i in range(1, size):
            dp[i] = max(dp[i-1]+nums[i], nums[i])   # 求出以每个元素nums[i]为结尾的连续子数组的最大和

        return max(dp)
```

#### [152. 乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/) （中等）

- 该题与上面的和不一样的地方是：

  - 负数乘以负数，可能称为最大值
  - 因此，要dp数组要保存（最大数，最小数）

- 与上题一样，相乘后，可能更小/更大，因此，dp函数要包括nums[i]

  ```python
  dp[i][0] = min(mul1, mul2, nums[i])
  dp[i][1] = max(mul1, mul2, nums[i])
  ```

  

```python
class Solution(object):
    def maxProduct(self, nums):
        if nums == []:
            return -1

        size = len(nums)

        # dp[i][0] 以nums[i]为结尾的最小乘积
        # dp[i][1] 以nums[i]为结尾的最大乘积
        dp = [[0, 0] for _ in range(size)]

        # 初始化
        dp[0][0], dp[0][1] = nums[0], nums[0]

        # 返回值
        ans_max = nums[0]

        for i in range(1, size):
            mul1 = dp[i-1][0] * nums[i]
            mul2 = dp[i-1][1] * nums[i]
            dp[i][0] = min(mul1, mul2, nums[i])
            dp[i][1] = max(mul1, mul2, nums[i])
            ans_max = max(ans_max, dp[i][1], dp[i][0])

        return ans_max
```



#### [300. 最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

- 关于子序列的题目，与子数组不同的是：
  - 在求当前nums[i]的dp[i]时，要for循环[0, i)区间内的数，根据题意，更新dp[i]

```python
class Solution(object):
    def lengthOfLIS(self, nums):
        if nums == []:
            return 0

        size = len(nums)

        # dp表示以nums[i]为结尾的LIS的长度
        dp = [1] * size

        for i in range(1, size):
            for j in range(i):
                if nums[i] > nums[j]:  # 当前元素 > 前一个元素
                    dp[i] = max(dp[j]+1, dp[i])
        
        return max(dp)
```

---

