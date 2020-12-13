#### [盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

- 注意点

  >  长度：r-l，不是r-l+1
  >
  > height[l], height[r]：谁小，就放弃谁

```python
class Solution(object):
    def maxArea(self, height):
        max_ans = 0
        
        l,r = 0,len(height)-1

        while l<r:
            area = min(height[l], height[r]) * (r-l)
            max_ans = max(max_ans, area)
            if height[r] > height[l]:
                l+=1
            else:
                r-=1
        return max_ans
```



---



#### 接雨水

可以说，接雨水，是最简单的**“困难”**级别的题了

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int size = height.size();

        if(size == 0)
            return 0;

        vector<int> left(size, 0);  // left[i]表示[0,i]的最大值
        vector<int> right(size, 0); // right[i]表示(size,i]的最大值

        // 初始化left
        left[0] = height[0];
        for (int i = 1; i<size; i++){
            left[i] = max(left[i-1],height[i]);
        }

        // 初始化right
        right[size-1] = height[size-1];
        for (int i = size-2; i>=0; i--){
            right[i] = max(right[i+1],height[i]);
        }

        int ret = 0;
        for(int i = 0; i < size; i++){
            ret += min(left[i], right[i]) - height[i]; // - 柱子高度
        }

        return ret;
    }
};
```

