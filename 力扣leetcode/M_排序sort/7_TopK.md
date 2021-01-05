- 堆排的应用

---


- ### N个`有序数组`整体最大的TopK

```c
例如，输入含有N行元素的二维数组可以代表N个一维数组
219，405，538，845，971
148，558
52，99，348，691
再输入整数k=5，则打印：
Top5: 971，845，691，558，538

核心：PQ中存放的元素pair<int,int>--><元素值, 元素来自哪个数组>

1.  构建一个大小为N的大顶堆<元素值，元素来自哪个数组>
2.  取出并打印当前堆顶元素top，必然是最大值，获得top来自哪个数组top.second
3.1 如果vec[top.second].size()!=0--->将vec[top.second]的下一个元素继续入队
3.2 如果vec[top.second]==0,不操作
4.  执行步骤2~3，直到打印前K个。
```



```c
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

vector<int> getTopK(vector<vector<int>> vec,int K){
	vector<int> ans;
	priority_queue<pair<int,int>> PQ;  // pair: <nums[i], 该数所属第几个数组>
    
	//用vec[i].back()，每个数组的最大的数，建立大根堆PQ
	for (int i = 0; i < vec.size(); i++){
        // 将每个有序数组最后一个元素插入PQ & 弹出
		PQ.emplace(vec[i].back(), i); vec[i].pop_back(); 
	}
    
	while(!PQ.empty())
    {
		pair<int, int> top = PQ.top(); PQ.pop(); //弹出PQ堆顶元素top
		ans.push_back(top.first);
        
        if (--K == 0) {
            break;
        }

		int index = top.second;
		if (!vec[index].empty()){ // 数组不为空
			PQ.emplace(vec[index].back(), index); // 数组最后一个元素插入PQ & 弹出
			vec[index].pop_back();
		}
	}
    
	return ans;
}
//求K个数组的前K大数
int main(){
	vector<vector<int>> vec = {
		{ 2, 5, 6, 6, 8, 10 },
		{ 1, 5, 6, 9 },
		{ 1, 4, 7, 7 }
	};
	vector<int> ret = getTopK(vec,5);
	for (auto node : ret)
		cout << node << endl;
}
```



---



- #### [23. 合并K个`升序链表`](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

  > 给你一个链表数组，每个链表都已经按升序排列。
  >
  > 请你将所有链表合并到一个升序链表中，返回合并后的链表。

```c
定义：priority_queue<Type, Container, Functional>
Type        数据类型
Container   容器类型
Functional  比较方式
```



```c
class Solution {
public:
    struct cmp{
        int operator()(ListNode* a, ListNode* b){ 
            return a->val > b->val;
        }
    };
    
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode dummy;
        ListNode* cur = &dummy;
        
        priority_queue<ListNode*, vector<ListNode*>, cmp> pq; // 创建小根堆
        // 将第一个元素加入pq堆
        for (auto elem : lists){ // elem是ListNode*，链表头节点
            if(elem){
                pq.push(elem);
            }
        }

        while (!pq.empty()){
            ListNode* top = pq.top(); pq.pop(); // 弹出堆顶(一定是最小值)
            if(top->next){  // 将节点的next再次插入堆中
                pq.push(top->next);
            }

            // 堆顶top添加到结果链表
            cur->next = top;
            cur = cur->next;
        }
        return dummy.next;
    }
};
```

