#### 单调队列、单调栈

解题套路：先分析题意，是否要使用单调栈、单调队列，并知道数据的变化过程（代码的书写是有规律的，按照下面的写法，代码十分清晰）

```python
1. 从前向后，遍历每一个元素 (该元素一定会进入栈/队列)
	for r in range(size):
2. while循环, 保持单调性
	2.1. while 循环条件 and 题目条件
    		从[]中移除不满足单调性的栈顶/队尾，即:pop(-1)
	2.2. 当出 单调[] 后，可能需要(保存结果 + 更新条件)
3. ...
4. 元素进入 单调[]
	4.1. 当进入 单调[] 后，可能需要(更新结果 + 更新条件)
```



---



#### [剑指 Offer 59 - I. 滑动窗口的最大值](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

:small_airplane: 单调队列

```python
class Solution(object):
    def maxSlidingWindow(self, nums, k):
        size = len(nums)
        ans = []
        queue = []  # 单调队列 (由大到小), 存放了【下标】
        for r in range(size):  # 循环遍历
            # 1. 保持队列的单调性
            while queue and nums[r] > nums[queue[-1]]: # [5,3,2], 进入4, 要依次删除[2,3]
                queue.pop(-1)
            # 2. 删除过期头部
            if queue and r-queue[0]+1 > k:  # 删除5
                queue.pop(0)
            # 3. 元素入队列
            queue.append(r)
            # 4. 加入结果集合
            if r+1 >= k:
                ans.append(nums[queue[0]])  # 当前窗口的最大值: nums[queue[0]]
        return ans
```



#### [LeetCode 面试题59 - II. 队列的最大值](https://segmentfault.com/a/1190000021962984):slightly_smiling_face:

要求：获取队列的最大值的时间复杂度为O(1)



---



------

:smile_cat:下面的题目都是 `单调栈`

#### [739. 每日温度](https://leetcode-cn.com/problems/daily-temperatures/):slightly_smiling_face:

```python
请根据每日气温列表，重新生成一个列表。对应位置的输出为：要想观测到更高的气温，至少需要等待的天数。如果气温在这之后都不会升高，请在该位置用 0 来代替。
例如，
	给定一个列表 temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
	你的输出应该是 [1, 1, 4, 2, 1, 1, 0, 0]。
```

栈中存放的元素，是下标，不是nums[i]

```python
class Solution(object):
    def dailyTemperatures(self, T):
        ans = [0] * len(T)  # 保存结果[0, 0, 0, ... ...]
        stack = []  # 单调栈, 存放【下标】
        for i in range(len(T)):
            while stack and T[stack[-1]] < T[i]:  # 栈顶温度 < 当前温度
                ans[stack[-1]] = i - stack[-1]    # 【出栈时】, 更新栈顶(下标位置)的结果值
                stack.pop()
            stack.append(i)
        return ans
```



#### [402. 移掉K位数字](https://leetcode-cn.com/problems/remove-k-digits/)

给定一个以字符串表示的非负整数 *num*，移除这个数中的 *k* 位数字，使得剩下的数字最小。

```
输入: num = "1432219", k = 3
输出: "1219"
解释: 移除掉三个数字 4, 3, 和 2 形成一个新的最小的数字 1219	。
```

栈中存放的元素是 num[i]，不是下标

```python
class Solution(object):
    def removeKdigits(self, num, k):
        stack = []
        for e in num:
            while stack and stack[-1] > e and k:  # 单调栈 (相反)
                stack.pop()
                k -= 1
            stack.append(e)  # 始终都将当前元素加入栈
        ret = stack[:-k] if(k > 0) else stack
        return "".join(ret).lstrip('0') or '0'  # 最后，stack中剩下的元素，穿起来，就是结果
```



#### [496. 下一个更大元素 I](https://leetcode-cn.com/problems/next-greater-element-i/)

给定两个 `没有重复元素` 的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。找到 nums1 中每个元素在 nums2 中的下一个比其大的值。

nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出 -1 。

```
输入: nums1 = [4,1,2], nums2 = [1,3,4,2].
输出: [-1,3,-1]
解释:
    对于num1中的数字4，你无法在第二个数组中找到下一个更大的数字，因此输出 -1。
    对于num1中的数字1，第二个数组中数字1右边的下一个较大数字是 3。
    对于num1中的数字2，第二个数组中没有下一个更大的数字，因此输出 -1。
```



```python
class Solution(object):
    def nextGreaterElement(self, nums1, nums2):
        stack = []
        hash = {}
        for e in nums2:
            while stack != [] and stack[-1] < e:  # 单调栈
                hash[stack[-1]] = e  # 将<元素,第一个比元素大的值>保存到哈希表
                stack.pop()
            stack.append(e)
        ret = []
        for e in nums1: # 遍历每个元素，从哈希表中找其对应的第一个较大值
            if e in hash.keys():
                ret.append(hash[e])
            else:
                ret.append(-1)
        return ret
```



#### [503. 下一个更大元素 II](https://leetcode-cn.com/problems/next-greater-element-ii/)

给定一个循环数组（最后一个元素的下一个元素是数组的第一个元素），输出每个元素的下一个更大元素。数字 x 的下一个更大的元素是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 -1。

```shell
输入: [1,2,1]
输出: [2,-1,2]
解释: 第一个 1 的下一个更大的数是 2；
数字 2 找不到下一个更大的数； 
第二个 1 的下一个最大的数需要循环搜索，结果也是 2。
```



```python
class Solution(object):
    def nextGreaterElements(self, nums):
        db_nums = nums * 2  # 将两个数组拼接
        size = len(nums)    # 原数组长度
        ret = [-1] * size   # 初始化结果[-1, -1, -1, ... ...]
        stack = []  # 栈 (栈中存放着下标)
        for i in range(size * 2):
            while stack != [] and db_nums[stack[-1]] < db_nums[i]:
                ret[stack[-1]] = db_nums[i]  # 出栈钱，保存结果
                stack.pop()
            if i < size: # 虚拟节点不入栈
                stack.append(i)
        return ret
```

