滑动窗口的要点

- 每次来一个新元素nums[i]，无论如何都要append到win中
- 入窗口/出窗口，要更新窗口的状态信息

python语法，弹出list的首元素：list.pop(0)

---



#### 无重复字符的最长字串 :slightly_smiling_face:

给定一个字符串，请你找出其中不含有重复字符的最长子串的长度。

```python
class Solution(object):
    def lengthOfLongestSubstring(self, s):
        ans = 0
        hash = {}
        l = 0
        for r in range(len(s)):
            if s[r] in hash:
                l = max(l, hash[s[r]]+1)  # 更新左下标 = 当前左下标的位置+1
            hash[s[r]] = r         # 当前新元素进入hash
            ans = max(ans, r-l+1)  # 更新结果ans
        return ans
```



#### strstr

```c++
// 判断haystack是否包含needle
int cmp(const char *haystack, const char *needle) 
{
    int subLen = strlen(needle);
    int mainLen = strlen(haystack);

    if (subLen > mainLen)
        return 0;

    while(*haystack && *needle){   // != '\0'
        if (*haystack != *needle){
            return 0;
        }
        else{
            haystack++;
            needle++;
        }
    }
    return 1;
}

int strStr(char * haystack, char * needle)
{
    int subLen = strlen(needle);
    int mainLen = strlen(haystack);

    if (subLen == 0 && mainLen == 0)
        return 0; 

    if (subLen > mainLen)
        return -1;

    for (int i = 0; i < mainLen; i++){
        if (i+subLen > mainLen){  // 超过窗口大小
            return -1;
        }
        if(cmp(haystack+i, needle) == 1)
            return i;
    }
    return -1;
}
```



---

#### 滑动窗口的最大值

（单调递减队列）

- 注意点
  - 单调队列中，保存的是`数组下标index`，而不是数组值nums[index]
  - 什么时候，ans中添加结果？ 窗口大小等于k时，即：当 r-1 >= k 时
- 步骤
  1. 索引r从0开始，一直向后滑动
  2. 首先，必须保证队列的单调性
     - 踢除 nums[r] > nums[queue[-1]] 的 nums[queue[-1]]
  3. 然后，删除过期队头，保证窗口的定长性（因为r进入窗口后，可能会造成窗口长度超过定长k）
     - r-`queue[0]`+1 > k，要剔除队头
  4. 元素入队：queue.append(r)
  5. 保存结果，即保存滑动窗口的最大值：当`r-l > k`时，ans.append(nums[queue[0]])

```python
class Solution(object):
    def maxSlidingWindow(self, nums, k):
        ans = []
        queue = []  # 单调队列(由大到小), 保存了“下标”
        for r in range(len(nums)):
            # 1. 保证队列单调
            while queue != [] and nums[r] > nums[queue[-1]]:  # 当前元素nums[r] > 队尾元素: 移除队尾
                queue.pop(-1)
            # 2. 删除过期队头元素  [注]: 2,3步骤可交换
            if queue != [] and r-queue[0]+1 > k:
                queue.pop(0)
            # 3. r进入窗口
            queue.append(r)
            # 4. 当r+1 >= k时, 此时必然形成窗口,保存结果
            if r+1 >= k:
                ans.append(nums[queue[0]])
        return ans
```



#### 59-队列的最大值
