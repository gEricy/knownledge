#### [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

数字 *n* 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

```shell
输入：n = 3
输出：[
       "((()))",
       "(()())",
       "(())()",
       "()(())",
       "()()()"
     ]
```

生成n对括弧，那么结果就是2n，设置left/right两个变量，分别记录左/右括弧出现的次数

> 每次，选中左/右括弧，相应的left/right都+1。当满足left+right=2n时，得到一个括弧

> 「注」在选择左/右括弧时，是有条件的！

> 「注」每次选中，都要对left/right +1，然后，将括弧加入land中，再次backtrace

> 1. 选中左括弧条件：left < n
> 2. 选中右括弧条件：left > right && right < n

```python
class Solution(object):
    # 1.左括弧+右括弧，共有2n个括弧时，一共能构成哪些
    # 2.不满足括弧匹配的进行剪枝
    def generateParenthesis(self, n):
        ans = []
        land = ""

        def backtrace(land, left, right): # left,right 左括弧数量, 右括弧的数量
            if left + right == 2*n:  # 递归终止条件: 左右括弧总数量 == 2n
                ans.append(land[:])
                return

            # 添加‘(’的条件: 左括弧 < n
            if left < n: 
                backtrace(land+'(', left+1, right)
                
            # 添加‘)’的条件: 左括弧>右括弧 && 右括弧 < n
            if left > right and right < n:  
                backtrace(land+")", left, right+1)

        backtrace(land, 0, 0)
        return ans
```



#### [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

```c
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

```python
class Solution(object):
    hash = {
        '2' : "abc",
        '3' : "def",
        '4' : "ghi",
        '5' : "jkl",
        '6' : "mno",
        '7' : "pqrs",
        '8' : "tuv",
        '9' : "wxyz"
    }
    
    def letterCombinations(self, digits):
        if digits == "":
            return []

        digits_list = []
        size = len(digits)
        
        # 将数字字符串，解析成键盘上对应的字母字符串, "23" --> ["abc", "def"]
        # digits --> digits_list
        for d in digits:  
            digits_list.append(self.hash[d])
        
        ret = []
        land = ""
        def backtrace(land, digits_list, idx):   # idx表示第几个字符串
            if len(land) == size:  # if idx == size:
                ret.append(land)
                return

            for ch in digits_list[idx]: 
                backtrace(land + ch, digits_list, idx+1) # 选择列表: 第一次从digits_list[0]选择一个, 递归时, 从digits_list[1]中选择一个, ... ...
        
        backtrace(land, digits_list, 0)
        return ret
```



#### [93. 复原IP地址](https://leetcode-cn.com/problems/restore-ip-addresses/)

```
输入：s = "25525511135"
输出：["255.255.11.135","255.255.111.35"]

输入：s = "0000"
输出：["0.0.0.0"]

输入：s = "1111"
输出：["1.1.1.1"]
```

