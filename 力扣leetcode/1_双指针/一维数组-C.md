#### 删除数组中的重复项

给定一个排序数组，你需要在`原地`删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

```C++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int size = nums.size();
        if (size == 0)
            return 0;

        int i = 0, j = 0;  // 起始下标相同，从0开始
        for (; j<size; j++){
            if(nums[i] != nums[j]){  // 不相等时
                nums[++i] = nums[j];
            }
        }
        return i+1;  // 返回值：数组长度
    }
};
```

#### 删除指定元素

```C++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int size = nums.size();
        int i = 0, j = 0;  // 起始下标相同，从0开始
        for (; j<size; j++){
            if(nums[j] != val){
                nums[i++] = nums[j];
            }
        }
        return i;
    }
};
```



#### 移动零

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的`末尾`，同时保持非零元素的相对顺序。

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int i = 0, j = 0;
        for (; j<nums.size(); j++){
            if (nums[j] != 0){
                nums[i++] = nums[j];
            }
        }
        while(i < nums.size()){
            nums[i++] = 0;
        }
    }
};
```

#### 合并两个有序数组

```c
输入：
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

输出：[1,2,2,3,5,6]
```

```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int size = m+n-1;
        int r1 = m-1, r2 = n-1;
        while(r1>=0 && r2>=0){  // 注意：循环条件 >= 0
            if (nums1[r1] > nums2[r2]){
                nums1[size--] = nums1[r1--];
            }
            else{
                nums1[size--] = nums2[r2--];
            }
        }
        while(r2>=0){  // 特殊处理: 如果第二个数组中元素有残留，要拷贝到nums1中
            nums1[size--] = nums2[r2--];
        }
    }
};
```

#### 荷兰国旗

使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

```c++
class Solution {
public:
    void swap(int& a, int& b){
        int t=a;a=b;b=t;
    }
    void sortColors(vector<int>& nums) {
        int size = nums.size();
        int i = 0, l = 0, r = size-1;
        while(i <= r) {  // 循环条件
            switch(nums[i]) {
            case 0: // 碰到0，交换，l/i都向前走
                swap(nums[l++], nums[i++]);
                break;
            case 1:  // 碰到1，直接向前走
                i++;
                break;
            case 2:
                swap(nums[r--], nums[i]); // 碰到2，交换，只r向前走，i不变
                    		// i不++, 因为可能是2和0换，换完之后0要再换到最初的位置
                break;
            }
        }
    }
};
```

#### 
