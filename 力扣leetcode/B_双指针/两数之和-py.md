#### 两数之和

- 给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

```python
class Solution(object):
    def twoSum(self, nums, target):
        hash = {}  # hash表中存放的是 [nums[i], i], 即 [元素值, 下标]
        for i in range(len(nums)):
            diff = target - nums[i]  # 差值
            if diff in hash.keys():
                return [i, hash[diff]]
            else:
                hash[nums[i]] = i
        return []
```

说明：这个题，不可以使用双指针去解，使用下面的代码会漏场景。==> 解法还是采用上面的 👆

```python
class Solution(object):
    def twoSum(self, nums, target):
		nums.sort()  # 先来排序
        l,r = 0,len(nums)-1  # 双指针
        while l < r:
            sum = nums[l]+nums[r]
            if sum==target:
                return [l,r]
            elif sum > target:
                r-=1
            else:
                l+=1
        return []
```

#### 三数之和

- 给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。
  - 注意：答案中不可以包含重复的三元组。

```python
class Solution(object):
    def threeSum(self, nums):
        # 两数之和扩展: 固定一个数nums[i]，求剩下区间[i+1, nlen-1]的两数之和
        ans = []
        nlen = len(nums)
        nums.sort() # 去重, 一定要先排序

        for i in range(nlen-2): # 固定一个值
            if i>0 and nums[i]==nums[i-1]:  # 去重1 (固定的数不为同一个)
                continue
            # 固定两个头尾指针l,r = [i+1, size)
            l, r = i+1, nlen-1  
            while l<r:
                sum = nums[i]+nums[l]+nums[r]
                if sum == 0:
                    ans.append([nums[i], nums[l], nums[r]])
                    l+=1
                    while l<r and nums[l]==nums[l-1]:  # 去重2
                        l+=1
                elif sum > 0:
                    r-=1
                else:
                    l+=1
        return ans
```

- 去重前提条件是有序，去重点见下
  - 去重1：固定的数不为同一个，i>0 and nums[i]==nums[i-1]，如：[-1],-1,0,1，消除多次-1,-1
  - 去重2：当sum=nums[i]+nums[l]+nums[r]=0时，l<r and nums[l]!=nums[l-1]，如：[-1],0,0,0,1。消除多次0,0,0
