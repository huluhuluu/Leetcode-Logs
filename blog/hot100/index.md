---
title: "LeetCode Hot100 记录"
date: 2026-02-26T12:00:00+08:00
lastmod: 2026-02-26T12:00:00+08:00
draft: false
description: "记录 LeetCode Hot100 的 C++ 和 Python 解题"
slug: "leetcode-hot100"
tags: ["LeetCode", "算法", "刷题", "Hot100"]
categories: ["刷题日记"]
comments: true
math: true
---

# Hot100
记录[LeetCode Hot100](https://leetcode.cn/problem-list/LTRv2Gcc/)的cpp和python解题

## [1] 相交链表
### 1. 题目
[相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/description/?envType=problem-list-v2&envId=LTRv2Gcc):给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表不存在相交节点，返回 null 。

题目数据 保证 整个链式结构中不存在环。
注意，函数返回结果后，链表必须 保持其原始结构 。
### 2. 思路
#### 2.1 思路1：两次遍历
非常经典的链表找相交题，相交节点后的节点是相同的节点，这部分长度一致，那么长的链表先走差值步数，这样两个链表剩余部分长度一致了，两个链表一起移动，直到找到相同的节点或者都走到末尾就可以找到相交节点。
#### 2.2 思路2：快慢指针
设公共长度为c, 链表1长度为a+c,链表2长度为b+c, 如果链表1走完后再走a步，链表2走完后再走b步，那么两个链表都走了a+b+c步，剩余第二次链表长度都是c，两个链表此时走到相交节点。如果没相交，那么两个链表走完后都是null。
### 3. 代码
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        // 获取长度
        int len1 = 0, len2 = 0;
        ListNode* tmpA=headA, *tmpB = headB;
        while(tmpA!=nullptr) tmpA=tmpA->next, len1++;
        while(tmpB!=nullptr) tmpB=tmpB->next, len2++;
        // 把长的链表换到tmpA 
        tmpA=headA, tmpB = headB;
        if(len1<len2){
            swap(len1, len2), swap(tmpA, tmpB);
        }
        // 做差
        int dl = len1-len2;
        while(dl){
            tmpA = tmpA->next, dl--;
        }
        // 两个链表同步移动
        while(tmpA!=nullptr){
            if(tmpA==tmpB){
                break;
            }
            tmpA=tmpA->next, tmpB=tmpB->next;
        }
        return tmpA;
    }
};
```
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> Optional[ListNode]:
        tmpA, tmpB, cntA, cntB = headA, headB, 0, 0
        while tmpA != tmpB:
            if tmpA.next==None and cntA==0:
                cntA, tmpA = 1, headB
            else:
                tmpA = tmpA.next
            if tmpB.next==None and cntB==0:
                cntB, tmpB = 1, headA
            else:
                tmpB = tmpB.next
        return tmpA
```
### 4. 学习
思路二更优雅的写法：
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> Optional[ListNode]:
        if headA==None or headB==None:
            return None
        tmpA, tmpB = headA, headB
        while tmpA != tmpB:
            tmpA, tmpB = tmpA.next if tmpA!=None else headB, tmpB.next if tmpB!=None else headA
        return tmpA
```
## [2] 二叉树的最近公共祖先
### 1. 题目
[二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/description/?envType=problem-list-v2&envId=LTRv2Gcc): 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”
### 2. 思路
找两个节点的深度最深的父节点，可以考虑递归，深度向下“递”时查找当前节点的子树是否包括两个目标节点，往上“归”时，判断当前节点是否同时含有两个目标节点，并且判断是否。
### 3. 代码
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode*  res = nullptr;
    std::pair<int, int> findNode(TreeNode* root, TreeNode* p, TreeNode* q){
        if(root == nullptr) return {false, false};
        // “递”：查找左右子树 与当前节点 是否含目标节点
        std::pair<int, int> l = findNode(root->left, p, q);
        std::pair<int, int> r = findNode(root->right, p, q);
        r.first |= l.first | root == p;
        r.second |= l.second | root == q;
        // “归”：判断当前节点是否同时含两个目标节点
        // 并且因为自底向上的“归”，第一次出现的满足条件的节点就是答案
        if(r.first && r.second && res==nullptr){res = root;}
        return r;
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        findNode(root, p, q);
        return res;
    }
};
```
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        res = None
        def findNode(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> List(bool,bool):
            if root==None:
                return [False, False]
            l, r = findNode(root.left, p, q), findNode(root.right, p, q)
            r = [r[0] | l[0] | (root==p), r[1] | l[1] | (root==q)]
            nonlocal res # 声明外层函数作用域变量
            if r[0]==True and r[1]==True and res == None:
                res = root
            return r
        findNode(root, p, q)
        return res
        
```
### 4. 学习
我这里用一个`pair<int,int>`表示子树是否分别找到两个节点，可以只用`bool`表示是否同时找到两个节点。

## [3] 回文链表 
### 1. 题目
[回文链表](https://leetcode.cn/problems/palindrome-linked-list/description/?envType=problem-list-v2&envId=LTRv2Gcc)：给你一个单链表的头节点 head ，请你判断该链表是否为回文链表。如果是，返回 true ；否则，返回 false 。
### 2. 思路
#### 2.1 思路1
把所有值取出来判断是否是回文串
#### 2.2 思路2
原地反转前一半链表，然后双指针依次比较前一半链表和后一半链表，并且前一半链表在遍历时再次进行反转。

### 3. 代码
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        int len = 0;
        ListNode* tmp = head, *cur = nullptr, *n = nullptr;
        while(tmp) len++,tmp=tmp->next;

        // resever prev half nodes
        int idx = 0;
        cur = nullptr, n = head;
        while(idx<len/2){
            tmp = cur;
            cur = n;
            n = n->next;
            cur->next = tmp;
            idx++;
        }
        if(len%2) tmp = n->next;
        else tmp = n;
        ListNode* tmp1 = cur;
        // two point
        while(tmp!=nullptr && tmp1!=nullptr){
            if(tmp->val!=tmp1->val){
                return false;
            }
            tmp=tmp->next, tmp1 = tmp1->next;
        }
        // TODO: resver pre half
        return true;
    }
};
```
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def isPalindrome(self, head: Optional[ListNode]) -> bool:
        gh, res = head, True
        def trave(h):
            if h == None:
                return
            # 向下递
            trave(h.next)
            # 归时逆序比较
            nonlocal gh, res
            if gh.val != h.val:
                res = False
            gh=gh.next
        
        trave(head)
        return res

```
### 4. 学习
并发环境下，思路2有缺点：函数运行时需要锁定其他线程或进程对链表的访问，因为在函数执行过程中链表会被修改。

可以用递归方式实现，设置一个全局的头指针，递到末尾节点时在“归”时开始逐步比较和全局指针的值，补了python代码如上。
## [4] 每日温度
### 1. 题目
[每日温度](https://leetcode.cn/problems/daily-temperatures/description/?envType=problem-list-v2&envId=LTRv2Gcc)：给定一个整数数组 temperatures ，表示每天的温度，返回一个数组 answer ，其中 answer[i] 是指对于第 i 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 0 来代替。
### 2. 思路
维护一个有序队列，队列内容是（温度，出现天数），在每个位置把当前天加入队列，队列中温度低于该位置后的天数可以填上答案。

### 3. 代码
```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        int n = temperatures.size();
        vector<int> res(n,0);
        priority_queue<std::pair<int, int> > q;
        for(int i=0;i<n;i++){
            while(!q.empty()){
                std::pair<int, int> t = q.top();
                if(-t.first >= temperatures[i]){ // 当前温度不高于前面所有温度 
                    // 默认最大堆 取负变最小堆
                    q.push(std::make_pair(-temperatures[i], i));
                    break;
                }
                else{
                    q.pop();
                    res[t.second] = i - t.second;
                }
            }
            if(q.empty()){
                q.push(std::make_pair(-temperatures[i], i));
            }
        }
        // 处理找不到下一个更高温度的下标 由于初始化也可以不处理
        while(!q.empty()){
            std::pair<int, int> t = q.top();
            q.pop();
            res[t.second] = 0;
        }
        return res;
    }
};
```
```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        n = len(temperatures)
        res, q = [0 for i in range(n)], [] 
        for i in range(n):
            while len(q) > 0 and q[-1][0] < temperatures[i]:
                res[q[-1][1]] = i - q[-1][1]
                q.pop()
            
            q.append([temperatures[i], i])

        return res
```
### 4. 学习
单调栈，比自己想的有序队列简洁多了，开始想用数组+二分查找实现python版本，发现python版本的二分接口不会，查找了下:
```python
import bisect # 这个单词是二等分的意思 记忆单词++
bisect.bisect_left(arr, target) # 对应cpp lower_bound
bisect.bisect_right(arr, target) # 对应cpp upper_bound
# 自动插入并维持有序数组
bisect.insort_left(arr, x)
bisect.insort_right(arr, x)

# 顺手查了下优先队列接口:https://zhuanlan.zhihu.com/p/669887657
import heapq # 最小堆
heapify() # 将一个列表转换为最小堆。
heappush() # 向堆中添加元素。
heappop() # 从堆中弹出并返回最小元素。
heapreplace() # 弹出并返回最小元素，然后将新元素推入堆

# 队列可以直接用 list的pop(0) + append()两个接口模拟
```

## [5] 翻转二叉树
### 1. 题目
[翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/description/?envType=problem-list-v2&envId=LTRv2Gcc)：给你一棵二叉树的根节点 root ，翻转这棵二叉树，并返回其根节点。
### 2. 思路
这里的翻转是更换左右子树，直接往下递归 依交换每个节点的左右子树就行。
### 3. 代码
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(root == nullptr) return nullptr;
        TreeNode* a = invertTree(root->right), *b = invertTree(root->left);
        root->left = a, root->right = b;
        return root;
    }
};
```
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if root == None:
            return root
        root.left, root.right = self.invertTree(root.right), self.invertTree(root.left)
        return root 
```
### 4. 学习
这里注意cpp代码力不能直接把下面写成一行， C++ 逗号表达式规则是按顺序执行，而python的多重赋值本质是元组解包，具有原子性。
```cpp

root->left = invertTree(root->right), root->right = invertTree(root->left);
```

## [6] 最大正方形
### 1. 题目
[最大正方形](https://leetcode.cn/problems/maximal-square/description/?envType=problem-list-v2&envId=LTRv2Gcc)：在一个由 '0' 和 '1' 组成的二维矩阵内，找到只包含 '1' 的最大正方形，并返回其面积。
### 2. 思路
预处理每个顶点，记录这个顶点作为右上角时，这条边的最长值。然后遍历每个顶点，在行方向判断最长边长，再列方向上不断判断不超过该边长的情况小的最长边。时间复杂度是O(n^3), 这题数据量是n<=300, 做一些越界的剪枝可以勉强通过。
### 3. 代码
```cpp
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int m = matrix.size(), n = matrix[0].size();
        vector<vector<int> > arr(m+1, vector<int>(n+1,0));
        // 预处理 下标加1处理越界
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(matrix[i][j] == '1'){
                    arr[i+1][j+1] = arr[i+1][j] + 1;
                }
            }
        }

        int res = 0;
        for(int i=0;i<m;i++){
            if(i + res - 1 > m) break;
            for(int j=0;j<n;j++){
                if(matrix[i][j]=='0' || arr[i+1][j+1]<=res) continue;
                int tmp_l = arr[i+1][j+1];
                // std::cout<<i<<" "<<j<<" | "<<tmp_l<<endl;
                // 枚举边长
                for(int l = 2; l<=tmp_l; l++){
                    if(i+l-1 >= m || arr[i+l][j+1] <= res || tmp_l <= res){
                        tmp_l = 0;
                        break;
                    }
                    tmp_l = min(arr[i+l][j+1], tmp_l);
                }
                res = max(res, tmp_l);
            }
        }
        return res * res;
    }
};
```
```python
class Solution:
    def maximalSquare(self, matrix: List[List[str]]) -> int:
        m, n, res = len(matrix), len(matrix[0]), 0
        pre = [[0 for j in range(n+1)] for i in range(m+1)]
        # 预处理
        for i in range(m):
            for j in range(n):
                if matrix[i][j] == '1':
                    pre[i+1][j+1] = pre[i+1][j] + 1
        # 遍历顶点
        for i in range(m):
            if i + res >= m: # 行向下延申越界
                break
            for j in range(n):
                if j + 1 <= res or pre[i+1][j+1] <= res: # 列向前延申越界 | 当前点边长小于已有最长正方形
                    continue
                # 判断当前点作为右上角是否能够形成正方形
                print(f"{i},{j},{pre[i+1][j+1]}")
                tmp_l = pre[i+1][j+1]
                for l in range(2, pre[i+1][j+1]+1): # 遍历可能的边长
                    if i+l-1>=m: #  行方向越界
                        tmp_l = min(tmp_l, l-1)
                        break
                    if pre[i+l][j+1] < tmp_l: # 这个点长度小于最长正方形
                        if pre[i+l][j+1] <= res:
                            tmp_l = min(tmp_l, l-1)
                            break
                        else:
                            tmp_l = pre[i+l][j+1]
                res = max(res, tmp_l)
        return res * res
```
### 4. 学习
这个题目做剪枝老越界（题解说暴力也能过...），一看题解是动态规划，仔细一想确实，有点二维前缀和的感觉。看来还是把思路想仔细。
还有很损的一点是，这里传入的matrix是char类型，要仔细看类型。

贴一版python的动态规划
```python
class Solution:
    def maximalSquare(self, matrix: List[List[str]]) -> int:
        m, n, res = len(matrix), len(matrix[0]), 0
        dp = [[0 for j in range(n+1)] for i in range(m+1)]
        for i in range(m):
            for j in range(n):
                if matrix[i][j]=='1':
                    # 找规律 当前点作为右下角可以形成的正方形边长是：
                    # 上方位置的边长 + 1 和 左边位置的边长 + 1的最小值
                    # 但是为了保证形成的正方形的左上角不是空的 还得加入左上第一个位置的边长
                    dp[i+1][j+1] = min(dp[i][j+1] + 1, dp[i+1][j] + 1)
                    dp[i+1][j+1] = min(dp[i+1][j+1], dp[i][j]+1)
                    res = max(res, dp[i+1][j+1])
        return res * res
```

## [7] 数组中的第K个最大元素
### 1. 题目
[数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/description/)：给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。

请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

你必须设计并实现时间复杂度为 O(n) 的算法解决此问题。
### 2. 思路
维护一个单调栈,长度是K,遇到更大的值就入栈并且替换值。需要用无序容器实现O(nK)，有序容器是O(nlogK),排序O(nlogn), 排不出O(n)， 等看题解。（这个log后值系数不大小于5）
### 3. 代码
```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int> q;
        for(int i=0;i<k;i++){
            q.push(-nums[i]);
        }
        for(int i=k;i<nums.size();i++){
            int t = q.top();
            if(-t<nums[i]){
                q.pop();q.push(-nums[i]);
            }
        }
        return -q.top();
    }
};
```
```python
import heapq
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        arr = nums[0:k]
        heapq.heapify(arr)
        for i in range(k, len(nums)):
            t = heapq.heappop(arr)
            heapq.heappush(arr,max(t,nums[i]))
        return heapq.heappop(arr)
```
### 4. 学习
[灵神题解](https://leetcode.cn/problems/kth-largest-element-in-an-array/solutions/3799769/on-kuai-su-xuan-ze-suan-fa-pythonjavaccg-lh7c/)：类似快排的随机选择算法，最优情况的期望是O(n)。

补个代码，下面代码过不了数据，因为不是随机选择划分点，选择当前段数组最左边的值，被数据制裁了。需要修改为随机选点，灵神代码很精辟 优先替换选中的值和最左边的值，简化代码逻辑，并且最后交换值的位置是r位置，且l和r位置和划分点比较不能取等 否则会不均匀划分。 对应修改点注释标出。
```python
class Solution:
    def findKthLargest(self, nums: List[int], k: int) -> int:
        def partition(nums, left, right, k):
            # 随机选点 并且换到最左边
            # i = randint(left, right)
            # nums[i], nums[left] = nums[left], nums[i]
            pivot, l, r = nums[left], left, right
            while l<=r:
                while l<=r and nums[l]<=pivot: # nums[l]<pivot 这里不取等号 
                    l += 1
                while l<=r and nums[r]>=pivot: # nums[r]>pivot 这里不取等号
                    r -= 1
                if l <= r:
                    nums[l], nums[r] = nums[r], nums[l]
                    l, r = l+1, r-1
            # swap pivot
            # 交换右边点 保证正确性
            # nums[r], nums[left] = pivot, nums[r]
        #     if k == right - r + 1:
        #         return pivot
        #     if k < right - r + 1:
        #         return partition(nums, r+1, right, k)
        #     else:
        #         return partition(nums, left, r-1, k - (right - r + 1))
        # return partition(nums, 0, len(nums)-1, k)
            nums[l-1], nums[left] = pivot, nums[l-1]
            if k == right - (l-1) + 1:
                return pivot
            if k < right - (l-1) + 1:
                return partition(nums, l, right, k)
            else:
                return partition(nums, left, l-2, k - (right - (l-1) + 1))
        return partition(nums, 0, len(nums)-1, k)
```

## [8] 实现 Trie (前缀树)
### 1. 题目
[实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/description/?envType=problem-list-v2&envId=LTRv2Gcc)：Trie（发音类似 "try"）或者说 前缀树 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补全和拼写检查。

请你实现 Trie 类：

Trie() 初始化前缀树对象。
void insert(String word) 向前缀树中插入字符串 word 。
boolean search(String word) 如果字符串 word 在前缀树中，返回 true（即，在检索之前已经插入）；否则，返回 false 。
boolean startsWith(String prefix) 如果之前已经插入的字符串 word 的前缀之一为 prefix ，返回 true ；否则，返回 false 。
### 2. 思路
TODO:
### 3. 代码
```cpp

```
```python

```
### 4. 学习

## [9] 课程表
### 1. 题目
[课程表](https://leetcode.cn/problems/course-schedule/?envType=problem-list-v2&envId=LTRv2Gcc)：你这个学期必须选修 numCourses 门课程，记为 0 到 numCourses - 1 。

在选修某些课程之前需要一些先修课程。 先修课程按数组 prerequisites 给出，其中 prerequisites[i] = [ai, bi] ，表示如果要学习课程 ai 则 必须 先学习课程  bi 。

例如，先修课程对 [0, 1] 表示：想要学习课程 0 ，你需要先完成课程 1 。
请你判断是否可能完成所有课程的学习？如果可以，返回 true ；否则，返回 false 。
### 2. 思路
#### 2.1 思路1
拓扑排序题，每次选没有入边的节点，判断最后是否能排序所有节点。
#### 2.2 思路2
可以判断图有没有环，但是不会写。
### 3. 代码
```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<int> degree(numCourses, 0); // 入度
        vector<vector<int> > mp(numCourses); // 图
        queue<int> q;
        int vis = 0;
        // 建图
        for(auto& pre: prerequisites){
            mp[pre[1]].emplace_back(pre[0]);
            degree[pre[0]]++;
        }
        // 初始化无入度节点
        for(int i=0;i<numCourses;i++){
            if(degree[i]==0){
                q.push(i);
            }
        }
        // 拓扑排序
        while(!q.empty()){
            int id = q.front();
            q.pop();
            vis++;
            for(int next: mp[id]){
                if(degree[next]==0) continue;
                degree[next]--;
                if(degree[next]==0) q.push(next);
            }
        }
        return vis == numCourses;
    }
};
```
```python
class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        vis, mp, degree = 0, [[] for i in range(numCourses)], [0 for i in range(numCourses)]
        for pre in prerequisites:
            degree[pre[0]] += 1
            mp[pre[1]].append(pre[0])
        q = []
        for i in range(numCourses):
            if degree[i]==0:
                q.append(i)
        
        while len(q)!=0:
            id, vis = q.pop(0), vis + 1
            for next in mp[id]:
                if degree[next] > 0:
                    degree[next] -= 1
                    if degree[next] == 0:
                        q.append(next)
        return vis == numCourses
```
### 4. 学习
DFS 三色标记法 判断是否有环：若在 DFS 中遇到灰色节点 → 存在环
白色（0）：未访问
灰色（1）：正在访问（在当前 DFS 路径中）
黑色（2）：已访问完成
## [10] 反转链表
### 1. 题目
[反转链表](https://leetcode.cn/problems/reverse-linked-list/?envType=problem-list-v2&envId=LTRv2Gcc)：给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。
 
### 2. 思路
#### 2.1 思路1
递归：顺序遍历节点，“归”时把子节点的下一个节点设置成当前节点，顺序访问到末尾节点时需要记录末尾节点作为新链表的头节点。
#### 2.2 思路2
循环：顺序遍历节点的过程中就把遍历的节点反向。
### 3. 代码
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        // 思路1
        if(head == nullptr) return head;
        ListNode* cur = nullptr, *n = head;
        while(n!=nullptr){
            ListNode* tmp = cur;
            cur = n;
            n = n->next;
            cur->next = tmp;
        }
        return cur;
    }
};
```
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 思路2
        Head = None
        def reverse(head: Optional[ListNode]) -> Optional[ListNode]:
            if head == None:
                return None
            if head.next == None:
                nonlocal Head
                Head = head
                return head
            reverse(head.next).next = head
            return head
        reverse(head)
        # 思路2
        if head!=None:
            head.next = None
        return Head
```
### 4. 学习

## [11] 岛屿数量
### 1. 题目
[岛屿数量](https://leetcode.cn/problems/number-of-islands/?envType=problem-list-v2&envId=LTRv2Gcc)：给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。
### 2. 思路
#### 2.1 思路1
并查集，但是不会写
#### 2.2 思路2
深度优先搜索，从1出发把相邻的1都置为0，直到没有相邻的1了，岛屿数量加1。
### 3. 代码
```cpp
class UnionFind{
public:
    UnionFind(int mm, int nn, vector<vector<char>>& grid): m(mm), n(nn){
        parent.resize(m, vector<int>(n, 0));
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(grid[i][j]=='1'){
                    parent[i][j] = i * n + j;
                    cnt++;
                }
            }
        }
    }
    int find(int x, int y){
        int root = parent[x][y];
        if(root != x * n + y){
            root = find(parent[x][y]/n, parent[x][y]%n);
        }
        parent[x][y] = root;
        return root;
    }
    void unite(int x1, int y1, int x2, int y2){
        int root1 = find(x1, y1), root2 = find(x2, y2);
        if(root1 != root2){
            parent[root2/n][root2%n] = root1;
            cnt--;
        }
    }
    int getCnt(){return cnt;}
private:
    vector<vector<int>> parent;
    int cnt = 0;
    int m, n;
};

class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int m = grid.size(), n = grid[0].size();
        UnionFind u(m, n, grid);
        vector<int> dx = {-1, 0, 1, 0};
        vector<int> dy = {0, -1, 0, 1};
        auto f = [&](int x, int y){
            return 0<=x && x<m && 0<=y && y<n;
        };
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(grid[i][j]=='1'){
                    for(int d=0;d<4;d++){
                        int nx = i + dx[d], ny = j + dy[d];
                        if(f(nx, ny) && grid[nx][ny]=='1'){
                            u.unite(i,j,nx,ny);
                        }
                    }
                }
            }
        }
        return u.getCnt();
    }
};
```
```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        res, m, n = 0, len(grid), len(grid[0])
        vis = [[0 for j in range(n)] for i in range(m)]
        # 左上右下
        dir_x, dir_y = [-1, 0, 1, 0], [0, -1, 0, 1] 
        def judge(x: int, y: int):
            nonlocal m, n
            return 0<=x and x<m and 0<=y and y<n

        def dfs(x: int, y: int, flag: int):
            nonlocal m, n, grid
            if not judge(x,y) or grid[x][y]=='0' or vis[x][y]!=0:
                return
            vis[x][y] = flag 
            for i in range(4):
                dx, dy = x + dir_x[i], y + dir_y[i]
                dfs(dx, dy, flag)
        
        for i in range(m):
            for j in range(n):
                if vis[i][j]==0 and grid[i][j]=='1':
                    res += 1
                    dfs(i, j, res)
        
        return res
```
### 4. 学习
python的判断可以直接写`0<=x<m`, 这里标记可以原地使用grid。
并查集核心操作是，补了一版cpp代码：
- find(x)：查找元素 x 所在集合的代表元（根），查找过程中将路径上所有节点直接指向根。
- union(x, y)：将 x 和 y 所在的两个集合合并


## [12] 打家劫舍
### 1. 题目
[打家劫舍](https://leetcode.cn/problems/house-robber/?envType=problem-list-v2&envId=LTRv2Gcc)：你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

### 2. 思路
#### 2.1 思路1
标准的动态规划题，找规律：对于每个位置，偷或者不偷，如果偷了就不能偷前一个位置，如果不偷就可以偷前一个位置。直接对每天用2个状态记录。
### 3. 代码
```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int> >dp(n+1, vector<int>(2,0));
        // dp[i][0] 前一天没偷
        // dp[i][1] 前一天偷了
        for(int i=0;i<n;i++){
            // 前一天没偷今天偷
            dp[i+1][1] = dp[i][0] + nums[i];
            // 前一天偷了今天没偷 | 连续两天没偷
            dp[i+1][0] =  max(dp[i][1], dp[i][0]);
        }
        return max(dp[n][0], dp[n][1]);
    }
};
```
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        dp, n = [[0, 0] for i in range(len(nums) + 1)], len(nums)
        for i in range(n):
            # 今天不偷
            dp[i+1][0] = max(dp[i][0], dp[i][1])
            # 今天偷
            dp[i+1][1] = dp[i][0] + nums[i]
        return max(dp[n][0], dp[n][1])
```
### 4. 学习
动态规划也可以对每天用1个状态记录，`dp[i]=max(dp[i-1], dp[i-2]+nums[i])`，但是需要对前两天的状态进行初始化。

## [13] 多数元素
### 1. 题目
[多数元素](https://leetcode.cn/problems/majority-element/?envType=problem-list-v2&envId=LTRv2Gcc)：给定一个大小为 n 的数组 nums ，返回其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。
 
### 2. 思路
#### 2.1 思路1
直接计算每个元素出现的次数，时间复杂度O(n)，空间复杂度O(n)
#### 2.2 思路2
排序，排序后中间的元素一定是多数元素，时间复杂度O(nlogn)，空间复杂度O(1)。
#### 2.3 思路3
双指针，指针1是候选的多数元素，指针2是当前元素，如果和指针1相同，指针1继续移动，指针1的计数+1；如果不同指针1计数-1，并且指针1计数清0后 需要重新选定指针1的候选。原理是多数元素出现的次数大于其他元素的总和，所以指针1一定会停在多数元素上。
### 3. 代码
```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        if(nums.size()%2){
            return nums[nums.size()/2];
        }
        // 偶数情况 可能出现在前半和后半部分
        // 极限情况是 全在前半部分
        if(nums[0]==nums[nums.size()/2-1]){
            return nums[0];
        }
        // 否则取中间位置
        return nums[nums.size()/2];
    }
};
```
```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        p1, p2, cnt, n = 0, 1, 1, len(nums)
        while p2<n:
            if nums[p1]!=nums[p2]:
                cnt -= 1
            else:
                cnt += 1
            p2 += 1
            if cnt == 0:
                p1, p2, cnt = p2, p2 + 1, 1
        return nums[p1]
```
### 4. 学习
出现次数大于**⌊ n/2 ⌋**的，排序不用考虑偶数情况，直接取中间位置的元素就行了。
Boyer-Moore 投票算法就是思路3。
还可以使用分治法。

## [14] 除了自身以外数组的乘积
### 1. 题目
[除了自身以外数组的乘积](https://leetcode.cn/problems/product-of-array-except-self/?envType=problem-list-v2&envId=LTRv2Gcc)：给你一个整数数组 nums，返回 数组 answer ，其中 answer[i] 等于 nums 中除了 nums[i] 之外其余各元素的乘积 。

题目数据 保证 数组 nums之中任意元素的全部前缀元素和后缀的乘积都在  32 位 整数范围内。

请 不要使用除法，且在 O(n) 时间复杂度内完成此题。
 
### 2. 思路
#### 2.1 思路1
注意到题目的数据范围小，`-30 <= nums[i] <= 30`也就是答案最多是61种，打表记录下缺少每个值的乘法结果。时间复杂度O(n)，空间复杂度O(61)即O(1)。
#### 2.2 思路2
分别记录前缀和后缀乘积，时间复杂度O(n)，空间复杂度O(n)。
### 3. 代码
```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> mp(61, 1); // 除了乘以i的情况
        vector<int> vis(61, 0);
        for(int i=0;i<n;i++){
            for(int j=0;j<61;j++){
                if(nums[i]+30==j && vis[j] == 0){
                    vis[j] = 1;
                    continue;
                }
                mp[j] *= nums[i];
            }
        }
        vector<int> res;
        for(int i=0;i<n;i++){
            res.emplace_back(mp[nums[i]+30]);
        }
        return res;
    }
};
```
```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        res, pre, suf, n = [], [1] + [1 for i in nums], [1] + [1 for i in nums], len(nums)
        for i in range(n):
            pre[i+1] = pre[i] * nums[i]
            j = n - i - 1
            suf[j] = suf[j+1] * nums[j]
        for i in range(n):
            res.append(pre[i] * suf[i+1])
        return res
```
### 4. 学习
看见题目O(1)空间直接没想到前后缀，根据数据范围直接打表了，看了圈题解没人这么干。

## [15] 最小栈
### 1. 题目
[最小栈](https://leetcode.cn/problems/min-stack/?envType=problem-list-v2&envId=LTRv2Gcc)：设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

实现 MinStack 类:

MinStack() 初始化堆栈对象。
void push(int val) 将元素val推入堆栈。
void pop() 删除堆栈顶部的元素。
int top() 获取堆栈顶部的元素。
int getMin() 获取堆栈中的最小元素。
 
### 2. 思路
TODO: 
### 3. 代码
```cpp

```
```python

```
### 4. 学习

## [16] 乘积最大子数组
### 1. 题目
[乘积最大子数组](https://leetcode.cn/problems/maximum-product-subarray/?envType=problem-list-v2&envId=LTRv2Gcc)：给你一个整数数组 nums ，请你找出数组中乘积最大的非空连续 子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

测试用例的答案是一个 32-位 整数。

请注意，一个只包含一个元素的数组的乘积是这个元素的值。

### 2. 思路
动态规划，找规律，对于每个位置，可以考虑两个转移动作，拿或不拿；这里的状态比较麻烦，要考虑正值和负值，需要两个状态，一个记录正最大值，一个记录负的最小值。
分情况讨论：
- 如果当前值是正数，那么正最大值就是前一个位置的正最大值乘以当前值或者当前值本身；负最小值就是前一个位置的负最小值乘以当前值或者0（不拿）。
- 如果当前值是负数，那么正最大值就是前一个位置的负最小值乘以当前值或者当前值本身；负最小值就是前一个位置的正最大值乘以当前值或者当前值本身。

这里需要注意结果必须至少选一个数。问就是WA过
### 3. 代码
```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        // dp[i][0] 正数最大值
        // dp[i][1] 负数最小值 
        int n = nums.size(), res = nums[0];
        vector<vector<int>> dp(n+1, vector<int>(2,0));
        for(int i=0;i<n;i++){
            if(nums[i]>0){
                // 当前值是正数，正最大值就是前一个位置的正最大值乘以当前值或者当前值本身；负最小值就是前一个位置的负最小值乘以当前值或者0（不拿）。
                dp[i+1][0] = max(dp[i][0]*nums[i], nums[i]);
                dp[i+1][1] = min(dp[i][1]*nums[i], 0);
            }
            else{
                // 当前值是负数，正最大值就是前一个位置的负最小值乘以当前值或者当前值本身，或者不拿；负最小值就是前一个位置的正最大值乘以当前值或者当前值本身。
                dp[i+1][0] = max(dp[i][1]*nums[i], 0);
                dp[i+1][1] = min(dp[i][0]*nums[i], nums[i]);
            }
            if(i!=0) res = max(res, dp[i+1][0]);
        }
        return res;
    }
};
```
```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        res, n, dp = nums[0] ,len(nums), [[0, 0] for num in nums] + [[0,0]]
        for i in range(n):
            if nums[i]>=0:
                dp[i+1][0] = max(dp[i][0] * nums[i], nums[i])
                dp[i+1][1] = min(dp[i][1] * nums[i], 0)
            else:
                dp[i+1][0] = max(dp[i][1] * nums[i], 0)
                dp[i+1][1] = min(dp[i][0] * nums[i], nums[i])
            if i != 0:
                res = max(res, dp[i+1][0])
        return res
```
### 4. 学习
直接通过max和min 无需判断当前值是正数还是负数，代码更简洁。
```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        res, n, dp = nums[0] ,len(nums), [[0, 0] for num in nums] + [[0,0]]
        for i in range(n):
            dp[i+1][0] = max(dp[i][0] * nums[i], nums[i], dp[i][1] * nums[i])
            dp[i+1][1] = min(dp[i][0] * nums[i], nums[i], dp[i][1] * nums[i])
            if i != 0:
                res = max(res, dp[i+1][0])
        return res
```

## [17] 排序链表
### 1. 题目
[排序链表](https://leetcode.cn/problems/sort-list/description/?envType=problem-list-v2&envId=LTRv2Gcc)：给你链表的头结点 head ，请将其按 升序 排列并返回 排序后的链表 。

### 2. 思路
TODO: 
### 3. 代码
```cpp

```
```python

```
### 4. 学习

## [18] LRU 缓存
### 1. 题目
[ LRU 缓存](https://leetcode.cn/problems/lru-cache/?envType=problem-list-v2&envId=LTRv2Gcc)：请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
实现 LRUCache 类：
LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。
 
### 2. 思路
TODO: 
### 3. 代码
```cpp

```
```python

```
### 4. 学习

## [19] 环形链表 II
### 1. 题目
[环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/?envType=problem-list-v2&envId=LTRv2Gcc)：给定一个链表的头节点  head ，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。

不允许修改 链表。
 
### 2. 思路
#### 2.1 思路1
dfs搜索标记，有点像前面的三色标记。
#### 2.2 思路2
快慢指针，慢指针一次走1步，快指针一次走2步，如果有环快慢指针一定会相遇。并且每移动一次，快针比慢针多走1步，当快慢指针第一次相遇时，快指针多走的步数刚好是环的长度的倍数，数学表示一下，非环长a步，环长b步，快慢指针第一次相遇时，只能是快指针比慢指针多走1圈环的路径就是b步，也就是总共移动了b次，快指针在环的位置是2b-a,慢指针位置是b-a,具体位置需要对b取模。
此时再用一个指针从头开始走a步，慢指针同步走a步，相遇时就是环的入口节点即慢指针位置是(b-a+a)%b。

代码细节是，快慢指针必须移动一次再判断是否相遇。
### 3. 代码
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *p1 = head, *p2 = head;
        while(p2!=nullptr && p2->next!=nullptr){
            p1 = p1->next, p2 = p2->next->next;
            if(p1 == p2){
                break;
            }
        }
        if(p2==nullptr || p2->next==nullptr) return nullptr;
        ListNode* res = head;
        while(res != p1){
            res=res->next, p1=p1->next;
        }
        return res;
    }
};
```
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        p1, p2, res = head, head, head
        while p2!=None and p2.next!=None:
            p1, p2 = p1.next, p2.next.next
            if p1 == p2:
                break
        if p2==None or p2.next==None:
            return None
        while p1!=res:
            p1, res = p1.next, res.next
        return res
```

## [20] 环形链表
### 1. 题目
[环形链表](https://leetcode.cn/problems/linked-list-cycle/?envType=problem-list-v2&envId=LTRv2Gcc): 给你一个链表的头节点 head ，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。注意：pos 不作为参数进行传递 。仅仅是为了标识链表的实际情况。

如果链表中存在环 ，则返回 true 。 否则，返回 false 。

### 2. 思路
直接同上题，快慢指针判环路。
### 3. 代码
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* p1 = head, *p2 = head;
        while(p2!=nullptr and p2->next!=nullptr){
            p1 = p1->next, p2 = p2->next->next;
            if(p1 == p2) break;
        }
        return !(p2==nullptr || p2->next==nullptr);

    }
};
```
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        p1, p2 = head, head
        while p2!=None and p2.next!=None:
            p1, p2 = p1.next, p2.next.next
            if p1 == p2:
                break
        return not (p2==None or p2.next==None)
```
### 4. 学习

## [21] 单词拆分
### 1. 题目
[单词拆分](https://leetcode.cn/problems/word-break/?envType=problem-list-v2&envId=LTRv2Gcc):给你一个字符串 s 和一个字符串列表 wordDict 作为字典。如果可以利用字典中出现的一个或多个单词拼接出 s 则返回 true。

注意：不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。
 
### 2. 思路
候选词长度不长，直接O(n^2)模拟，用一个队列表示当前能拼接到的字符串长度，然后每次从队列中取出一个下标，遍历字典中的单词，如果能匹配上就把匹配上单词的末尾下标加入队列。直到队列为空或者能匹配到字符串末尾。
### 3. 代码
```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        map<int, int> vis;
        map<string, int> word;
        queue<int> q;
        int n = s.length();
        for(string& w: wordDict){
            word[w] = 1;
        }
        q.push(0);
        while(!q.empty()){
            int l = q.front();
            q.pop();
            string tmps = "";
            // 遍历所有可能字符串
            for(int nl=l+1; nl<=n; nl++){
                tmps += s[nl-1];
                // 新位置
                if(vis.find(nl)==vis.end() && word.find(tmps)!=word.end()){
                    q.push(nl);
                    vis[nl] = 1;
                }
            }
        }
        return vis.find(n)!=vis.end();
    }
};
```
```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        vis, mp, n, q = {}, {}, len(s), [0,]
        for word in wordDict:
            mp[word] = 1
        while len(q):
            l, tmp_s = q.pop(0), ""
            for nl in range(l+1, n+1):
                tmp_s += s[nl-1]
                if nl not in vis and tmp_s in mp:
                    q.append(nl)
                    vis[nl] = 1
        return n in vis
```
### 4. 学习
题解这题是动态规划，但是动态规划需要切分字符串，比较麻烦，直接一步步累加字符串感觉更顺手一些。这个方法更有点像BFS，队列里存的是当前能拼接到的字符串长度。

## [22] 只出现一次的数字
### 1. 题目
[只出现一次的数字](https://leetcode.cn/problems/single-number/?envType=problem-list-v2&envId=LTRv2Gcc):给你一个 非空 整数数组 nums ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。
 
### 2. 思路
直接利用异或的性质，把所异或起来，相同的数异或结果为0，0和任何数异或结果为这个数本身，所以最后的结果就是只出现一次的那个数。
### 3. 代码
```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int res = 0;
        for(int num: nums) res^=num;
        return res;
    }
};
```
```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        res = 0
        for num in nums:
            res ^= num
        return res
```

## [23] 回文子串
### 1. 题目
[回文子串](https://leetcode.cn/problems/palindromic-substrings/description/?envType=problem-list-v2&envId=LTRv2Gcc):给你一个字符串 s ，请你统计并返回这个字符串中 回文子串 的数目。

回文字符串 是正着读和倒过来读一样的字符串。

子字符串 是字符串中的由连续字符组成的一个序列。
 
### 2. 思路

### 3. 代码
```cpp
class Solution {
public:
    int countSubstrings(string s) {
        int n = s.length(), res = s.length();
        vector<vector<int> >dp(n, vector<int>(n,0));
        for(int i=0;i<n;i++) dp[i][i] = 1;
        for(int l=2; l<=n; l++){
            for(int i=0;i<n;i++){
                int j = i+l-1;
                if(j>=n) break;
                if(s[i]==s[j] && (l==2 || dp[i+1][j-1]) ){
                    dp[i][j]=1, res+=1;
                }
            }
        }
        return res;
    }
};
```
```python
class Solution:
    def countSubstrings(self, s: str) -> int:
        # O(n^2)
        # dp[i][j] s[i]==s[j] && dp[i+1][j-1]==1
        res, n, dp = len(s), len(s), [[0 for j in range(len(s))] for i in range(len(s))]
        # 初始化
        for i in range(n):
            dp[i][i] = 1
        # 动态规划
        for l in range(2, n+1):
            for i in range(n):
                j = i + l - 1
                if j >=n: break
                if s[i]==s[j] and (l==2 or dp[i+1][j-1]==1):
                    res += 1
                    dp[i][j] = 1
        return res
```
### 4. 学习
[Manacher 算法](https://oi-wiki.org/string/manacher/)：需要用到字符串哈希， TODO

## [24] 最长连续序列
### 1. 题目
[最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/description/?envType=problem-list-v2&envId=LTRv2Gcc):给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 O(n) 的算法解决此问题。
 
### 2. 思路
#### 2.1 思路1
有序容器、排序等复杂度是O(nlogn),哈希复杂度受限于数据范围$10^9$也比较打大不满足题目要求。并查集时间复杂度也不够。
### 3. 代码
```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_map<int, int> mp;
        for(int& num:nums) mp[num] = 1;
        int res = 0;
        for(auto &[key, val]: mp){
            if(mp.find(key-1)!=mp.end()) continue;
            int tmp = 1;
            while(mp.find(key+tmp)!=mp.end()) tmp++;
            res = max(res, tmp);
        }
        return res;
    }
};
```
```python
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        mp, res = {}, 0
        for num in nums:
            mp[num] = 1
        for key, val in mp.items():
            if (key-1) in mp:
                continue
            tmp = 1
            while (key + tmp) in mp:
                tmp += 1
            res = max(res, tmp)
        return res
```
### 4. 学习
脑子干抽了，题解就是桶哈希... 考点是遍历哈希表时保持O(n); 有点离谱，感觉像故意找事，数据范围这么大，如果哈希底层出现很多哈希冲突/哈希桶开的很大，时间复杂度也比较高。（对底层实现不太了解）
学习：https://zhuanlan.zhihu.com/p/718026506，底层是开链法，哈希冲突的值用链表串起来。

## [25] 二叉树中的最大路径和
### 1. 题目
[二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/?envType=problem-list-v2&envId=LTRv2Gcc): 二叉树中的 路径 被定义为一条节点序列，序列中每对相邻节点之间都存在一条边。同一个节点在一条路径序列中 至多出现一次 。该路径 至少包含一个 节点，且不一定经过根节点。

路径和 是路径中各节点值的总和。

给你一个二叉树的根节点 root ，返回其 最大路径和 。
 
### 2. 思路
深度优先搜索, 搜索每个节点的最大子路径和（通俗点说是一条路径会经过节点左子树的一部分和右子树的一部分以及顶点），这里搜索的结果是结果该节点的子树最大路径和。在每一步可以更新答案。
### 3. 代码
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int maxPathSum(TreeNode* root) {
        int res = INT_MIN;
        std::function<int(TreeNode*)> dfs;
        dfs = [&res, &dfs](TreeNode* root){
            if(root == nullptr) return 0;
            int l = dfs(root->left), r = dfs(root->right);

            res = max(max(l, 0) + max(r, 0) + root->val, res);
            return max(max(l, r), 0) + root->val;
        };
        dfs(root);
        return res;
    }
};
```
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def maxPathSum(self, root: Optional[TreeNode]) -> int:
        if root==None:
            return 0
        res = root.val
        # 返回值是一条子路径的最大值
        def trave(root):
            if root == None:
                return 0
            
            l, r = trave(root.left), trave(root.right)
            nonlocal res
            res = max(res, max(l, 0) + max(r, 0) + root.val)
            return max(max(l, r), 0) + root.val
        trave(root)
        return res
```
### 4. 学习
复杂题，复杂在特殊情况的判断，最大子路径可能是负数，可能不经过左子树或者右子树。
注意递归匿名函数的写法。

## [26] 零钱兑换
### 1. 题目
[零钱兑换](https://leetcode.cn/problems/coin-change/description/?envType=problem-list-v2&envId=LTRv2Gcc):给你一个整数数组 coins ，表示不同面额的硬币；以及一个整数 amount ，表示总金额。

计算并返回可以凑成总金额所需的 最少的硬币个数 。如果没有任何一种硬币组合能组成总金额，返回 -1 。

你可以认为每种硬币的数量是无限的。
 
### 2. 思路
直接动态规划了，和背包很像，dp[i]表示金额为i时最少的硬币个数，状态转移方程是dp[i]=min(dp[i], dp[i-coin]+1)。
### 3. 代码
```cpp
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount+1, INT_MAX);
        dp[0] = 0;
        for(int i=0;i<=amount;i++){
            if(dp[i]==INT_MAX) continue;
            for(int& c: coins){
                if((long)i+c>amount){
                    continue;
                }
                dp[i+c] = min(dp[i+c], dp[i]+1);
            }
        }
        return (dp[amount]!=INT_MAX)?dp[amount]:-1;
    }
};
```
```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        dp = [0] + [-1 for i in range(amount)]
        for i in range(amount+1):
            if dp[i] == -1:
                continue
            for c in coins:
                if c + i> amount:
                    continue
                if dp[i+c] != -1:
                    dp[i+c] = min(dp[i+c], dp[i]+1)
                else:
                    dp[i+c] = dp[i] + 1
        return dp[amount]
```
### 4. 学习
坏题，在数据范围上卡一个int溢出。

## [27] 目标和
### 1. 题目
[目标和](https://leetcode.cn/problems/target-sum/?envType=problem-list-v2&envId=LTRv2Gcc):给你一个非负整数数组 nums 和一个整数 target 。

向数组中的每个整数前添加 '+' 或 '-' ，然后串联起所有整数，可以构造一个 表达式 ：

例如，nums = [2, 1] ，可以在 2 之前添加 '+' ，在 1 之前添加 '-' ，然后串联起来得到表达式 "+2-1" 。
返回可以通过上述方法构造的、运算结果等于 target 的不同 表达式 的数目。
 
### 2. 思路
#### 2.1 思路1
搜索，暴力搜索每个位置的+和-，时间复杂度O(2^n)，这题数据量n=20,复杂度是$10^6$
#### 2.2 思路2
动态规划，和0-1背包类似，每个位置取正或-，
### 3. 代码
```cpp
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int n = nums.size(), res = 0;
        // C++23 
        auto dfs = [&](this auto&& self, int i, int val) -> void {
            if (i == n) {
                if (target == val) res += 1;
                return;
            }
            // 不需要传 self，直接调用self
            self(i + 1, val + nums[i]);
            self(i + 1, val - nums[i]);
        };
        dfs(0, 0);
        return res;
    }
};
```
```python
class Solution:
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
        dp, n = [{0:1}, {}], len(nums)
        for num in nums:
            for key, val in dp[0].items():
                # 取正和取负
                if (key+num) not in dp[1]:
                    dp[1][key+num] = 0
                if (key-num) not in dp[1]:
                    dp[1][key-num] = 0
                dp[1][key+num] += val
                dp[1][key-num] += val
            dp[0], dp[1] = dp[1], {}
        if target not in dp[0]:
            return 0
        return dp[0][target]

```
### 4. 学习
这里cpp匿名函数的递归需要显式自传递，间接调用自己的时间会被调用次数放大，下面代码就不能通过：
```cpp
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int n = nums.size(), res = 0;
        std::function<void(int, int)> dfs;
        dfs = [&](int i, int val){
            if(i==n){
                if(target==val) res+=1;
                return;
            }
            dfs(i+1, val+nums[i]);
            dfs(i+1, val-nums[i]);
        };
        dfs(0, 0);
        return res;
    }
};
```

## [28] 汉明距离
### 1. 题目
[汉明距离](https://leetcode.cn/problems/hamming-distance/description/?envType=problem-list-v2&envId=LTRv2Gcc): 两个整数之间的 汉明距离 指的是这两个数字对应二进制位不同的位置的数目。

给你两个整数 x 和 y，计算并返回它们之间的汉明距离
 
### 2. 思路
直接模拟，或者异或后统计1的个数。
### 3. 代码
```cpp
class Solution {
public:
    int hammingDistance(int x, int y) {
        int res = 0, tmp = x ^ y;
        while(tmp){
            res += (tmp&1);
            tmp >>= 1;
        }
        return res;
    }
};
```
```python
class Solution:
    def hammingDistance(self, x: int, y: int) -> int:
        tmp, res = x ^ y, 0
        while tmp > 0:
            res, tmp = res + (tmp & 1), tmp>>1
        return res
```
### 4. 学习
数1可以用位运算，补一版cpp代码：
```cpp
class Solution {
public:
    int hammingDistance(int x, int y) {
        int res = 0, tmp = x ^ y;
        while(tmp){
            res++, tmp = tmp & (tmp - 1);
        }
        return res;
    }
};
```
## [29] 找到所有数组中消失的数字
### 1. 题目
[找到所有数组中消失的数字](https://leetcode.cn/problems/find-all-numbers-disappeared-in-an-array/description/?envType=problem-list-v2&envId=LTRv2Gcc): 给你一个含 n 个整数的数组 nums ，其中 nums[i] 在区间 [1, n] 内。请你找出所有在 [1, n] 范围内但没有出现在 nums 中的数字，并以数组的形式返回结果。
 
### 2. 思路
- 哈希，时间O(n), 空间O(n)
- 排序，时间O(nlogn)，空间O(1)
- 原地哈希，把每个值交换到对应的位置，最后对不齐的位置就是缺少的数字，时间O(n)，空间O(1)，这里的细节是发现一个值不对需要一直交换到这个位置的值是对的或者待交换的值已经交换了。

### 3. 代码
```cpp
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        int n = nums.size();
        for(int i=0; i<n; i++){
            if(nums[i]==i+1) continue;
            int idx = i;
            while(nums[idx] != idx + 1 && nums[nums[idx] - 1] != nums[idx]){
                swap(nums[idx], nums[nums[idx] - 1]);
            }
        }
        vector<int> res;
        for(int i=0; i<n; i++){
            if(nums[i]!=i+1) res.push_back(i+1);
        }
        return res;
    }
};
```
```python
class Solution:
    def findDisappearedNumbers(self, nums: List[int]) -> List[int]:
        res, n = [], len(nums)
        for i in range(n):
            if nums[i] == i + 1:
                continue
            
            while nums[i] != i + 1 and nums[nums[i] - 1] != nums[i]:
                # nums[i], nums[nums[i]-1] = nums[nums[i]-1], nums[i] # false 这里左边的等式中先赋值了nums[i]，导致右边的nums[i]被修改了，所以需要先把nums[i]的值保存下来
                nums[nums[i]-1], nums[i] = nums[i], nums[nums[i]-1]
                
        for i in range(n):
            if nums[i] != i + 1:
                res.append(i+1)
        return res
```
### 4. 学习
python的连续赋值需要注意赋值顺序。
- 题解也很巧妙，把对应位置的值+n这样取模还能拿到原数据，并且判断>n就知道这个位置的值出现过了。


## [30] 找到字符串中所有字母异位词
### 1. 题目
[找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/description/?envType=problem-list-v2&envId=LTRv2Gcc):给定两个字符串 s 和 p，找到 s 中所有 p 的 异位词 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。
 
### 2. 思路
滑动窗口， 字符串长度比较长 不方便用字符串哈希，就正常哈记录窗口扫过的字符数。
### 3. 代码
```cpp
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        int l1 = s.length(), l2 = p.length();
        vector<int> mp(26, 0);
        if(l1<l2) return {};

        // 用1个字典记录 窗口相对于p的差值
        for(int i=0; i<l2; i++){
            mp[p[i]-'a']+=1, mp[s[i]-'a']-=1;
        }
        // 差值全为0表示是异位值
        auto check = [&mp](){
            for(int i=0;i<26;i++) if(mp[i]!=0) return false;
            return true;
        };
        vector<int> res;
        for(int prev=0; prev + l2 <=l1;prev++){
            if(check()) res.push_back(prev);
            int next = prev + l2;
            if(next < l1) mp[s[prev]-'a']++, mp[s[next]-'a']--;// 这里注意符号
        }
        return res;
    }
};
```
```python
class Solution:
    def findAnagrams(self, s: str, p: str) -> List[int]:
        mp, idx, l1, l2, res = {c:0 for c in "abcdefghijklmnopqrstuvwxyz"}, 0, len(s), len(p), []
        if l1<l2:
            return res
        # init
        for i in range(l2):
            mp[p[i]] += 1
            mp[s[i]] -= 1

        def check(d):
            for k, v in d.items():
                if v != 0:
                    return False
            return True

        for i in range(0, l1-l2+1):
            prev, n = i, i + l2
            if check(mp):
                res.append(prev)
            if n<l1:
                mp[s[prev]] += 1
                mp[s[n]] -= 1
        return res

```
### 4. 学习
python `ord chr` 函数可以把字符和ASCII码互相转换。
灵神题解的不定长滑窗解法很精彩，先移窗口右端点 使整体窗口满足情况，再移动左端点，精确满足情况。

## [31] 路径总和 III
### 1. 题目
[路径总和 III](https://leetcode.cn/problems/path-sum-iii/?envType=problem-list-v2&envId=LTRv2Gcc): 给定一个二叉树的根节点 root 和一个整数 targetSum ，求该二叉树里节点值之和等于 targetSum 的 路径 的数目。
路径 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。
 
### 2. 思路
直接在向下搜索的时候，把父节点的和传入。这里要特判一下不计算当前节点的子路径，不然可能被重复包含。
### 3. 代码
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int pathSum(TreeNode* root, int targetSum) {
        int res = 0;
        map<TreeNode*, int> mp;
        auto dfs = [&res, &mp, &targetSum](this auto&&self, TreeNode* root, long long s){
            if(root == nullptr) return;
            if(mp.find(root)==mp.end()){
                self(root->left, 0);
                self(root->right, 0);
            }
            mp[root] = 0;
            self(root->left, s + root->val);
            self(root->right, s + root->val);
            if(s + root->val == targetSum) res++;
        };
        dfs(root, 0);
        return res;
    }
};
```
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def pathSum(self, root: Optional[TreeNode], targetSum: int) -> int:
        res, mp = 0, {}
        def dfs(root, s):
            nonlocal res, targetSum, mp
            # print("nullptr" if not root else root.val, s, res)
            if root == None: 
                return
            
            if root not in mp:
                dfs(root.left, 0)
                dfs(root.right,0)
            mp[root] = 0
            dfs(root.left, s + root.val)
            dfs(root.right, s + root.val)
            if s + root.val == targetSum:
                res += 1
        dfs(root, 0)
        return res
```
### 4. 学习
可以做记忆化，但是dfs的状态得重新设计，题解的dfs设计的很优雅就：`rootSum(p,val) 表示以节点 p 为起点向下且满足路径总和为 val 的路径数目`。

## [32] 分割等和子集
### 1. 题目
[分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/description/?envType=problem-list-v2&envId=LTRv2Gcc):给你一个 只包含正整数 的 非空 数组 nums 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。
 
### 2. 思路
#### 2.1 思路1
类似背包问题，看能不能组合所有数字成sum/2, 这里的细节是如果sum必须是偶数。
### 3. 代码
```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0, n = nums.size();
        for(int& num:nums) sum+=num;
        if(sum%2) return false;
        vector<int> dp{0};
        map<int, int> mp;
        for(int i=0;i<n;i++){
            int len = dp.size();
            for(int j=0;j<len;j++){
                if(mp[nums[i] + dp[j]]) continue; 
                dp.push_back(nums[i] + dp[j]);
                mp[nums[i] + dp[j]] = 1;
                if(dp.back() == sum/2) return true;
            }
        }
        return false;
    }
};
```
```python
class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        n, s = len(nums), sum(nums)
        dp = [0 for i in range(s+1)]
        if s%2:
            return False
        dp[0] = 1
        for i in range(n):
            for k in range(s+1):
                # 这里得逆序
                j = s - k
                if dp[j] ==0 or nums[i] + j > s:
                    continue
                dp[j+nums[i]] = 1
        return bool(dp[s//2])
```
### 4. 学习

## [33] 根据身高重建队列
### 1. 题目
[根据身高重建队列](https://leetcode.cn/problems/queue-reconstruction-by-height/description/?envType=problem-list-v2&envId=LTRv2Gcc):假设有打乱顺序的一群人站成一个队列，数组 people 表示队列中一些人的属性（不一定按顺序）。每个 people[i] = [hi, ki] 表示第 i 个人的身高为 hi ，前面 正好 有 ki 个身高大于或等于 hi 的人。

请你重新构造并返回输入数组 people 所表示的队列。返回的队列应该格式化为数组 queue ，其中 queue[j] = [hj, kj] 是队列中第 j 个人的属性（queue[0] 是排在队列前面的人）。
 
### 2. 思路
TODO:
### 3. 代码
```cpp

```
```python

```
### 4. 学习

## [] 
### 1. 题目
[]():
 
### 2. 思路
### 3. 代码
```cpp

```
```python

```
### 4. 学习

## [] 戳气球
### 1. 题目
[戳气球](https://leetcode.cn/problems/burst-balloons/description/?envType=problem-list-v2&envId=LTRv2Gcc):有 n 个气球，编号为0 到 n - 1，每个气球上都标有一个数字，这些数字存在数组 nums 中。

现在要求你戳破所有的气球。戳破第 i 个气球，你可以获得 nums[i - 1] * nums[i] * nums[i + 1] 枚硬币。 这里的 i - 1 和 i + 1 代表和 i 相邻的两个气球的序号。如果 i - 1或 i + 1 超出了数组的边界，那么就当它是一个数字为 1 的气球。

求所能获得硬币的最大数量。
 
### 2. 学习

正向设置状态难，将全过程看作是每次添加一个气球。
TODO: 
### 3. 代码
```cpp

```
```python

```
### 4. 学习