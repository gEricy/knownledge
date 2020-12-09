[179. 最大组合数](https://leetcode-cn.com/problems/largest-number/)

```shell
输入：nums = [3,30,34,5,9]
输出："9534330"
```

```python
class Solution(object):
    def largestNumber(self, nums):
        nums.sort(lambda x,y : cmp(str(y)+str(x), str(x)+str(y)))  # 按照 y+x, x+y 的字典序排序
        
        ans = ""
        for elem in nums:
            ans += str(elem)
        
        ans = ans.lstrip('0')

        return '0' if ans == "" else ans
```

