---
title: C++库函数积累应用
tags:
  - 库函数
categories:
  - C++
date: 2025-5-13 15:00:00
excerpt: Some useful C++ function!
---
# C++库函数积累应用
## 字符串 (string) 相关
### 调整字符串数组长度函数（resize()）
+ 调整字符串数组长度的函数 `string::resize()`：
	+ 将字符串大小调整为 `n` 个字符的长度。
	+ 如果 `n` 小于当前字符串长度，则当前值将缩短为其第一个 `n` 个字符，删除超过 `n` 个字符的字符。
	+ 如果 `n` 大于当前字符串长度，则通过在末尾插入所需数量的字符来扩展当前内容，以达到 `n` 的大小。如果指定了 `char c`，则新元素初始化为 `char c` 的副本，否则，它们是值初始化字符（空字符）。

![string::resize()](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250513151409.png)

### 将数值类型转换为字符串类型（to_string()）
+ 将数值类型转换为字符串类型的函数 `to_string()` 。
+ 用法示例：
	+ 二叉树的所有路径：[257.二叉树的所有路径](https://leetcode.cn/problems/binary-tree-paths/description/)
+ 思路：这道题目要求从根节点到叶子的路径，所以需要前序遍历，这样才方便让父节点指向孩子节点，找到对应的路径。同时要使用到回溯把路径记录下来，需要回溯来回退一个路径再进入另一个路径。
+ 代码：

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
    void travelsal(TreeNode* node, string path, vector<string>& result){
        // 中，处理路径中的节点数值
        path += to_string(node->val);
        // 递归终止条件，遇到叶子节点
        if(node->left == nullptr && node->right == nullptr){
            result.push_back(path);
            return;
        }
        // 左，回溯需要在调用函数的path中加"->"
        if(node->left){
            travelsal(node->left, path + "->", result);
        }
        // 右，回溯需要在调用函数的path中加"->"
        if(node->right){
            travelsal(node->right, path + "->", result);
        }
    }
    
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> result;
        if(root == nullptr)
            return result;
        string path;
        travelsal(root, path, result);
        return result;
    }
};
```

### 获取字符串的子串（substr()）
+ 获取字符串的子串 `substr()`：

```cpp
string substr (size_t pos = 0, size_t len = npos) const;
```

+ `pos`：这是一个 `size_t` 类型的参数，表示子字符串开始的位置，其默认值为 `0`，即从字符串的起始位置开始提取。
+ `len`：同样是 `size_t` 类型的参数，代表要提取的子字符串的长度，默认值为 `std::string::npos`。当使用默认值时，函数会从 `pos` 位置开始提取直到字符串的末尾。
+ 示例题目：
	+ 分割回文串：[131.分割回文串](https://leetcode.cn/problems/palindrome-partitioning/description/)
	+ 代码：

```cpp
class Solution {
public:
    vector<vector<string>> result;
    vector<string> path;

    // 判断是否回文串
    bool isPalindrome(const string s, int start, int end){
        int i=start, j=end;
        while(i<=j){
            if(s[i] != s[j])
                return false;
            i++;
            j--;
        }
        return true;
    }

    void backtracking(string s, int startpoint){
        // 终止条件（startpoint能够到达字符串末尾说明一定是满足分割要求）
        if(startpoint>=s.size()){
            result.push_back(path);
            return;
        }
        // 递归回溯逻辑
        for(int i=startpoint; i<s.size(); i++){
            if(isPalindrome(s, startpoint, i)){
                // 获取[startpoint,i]在s中的子串
                string str = s.substr(startpoint, i-startpoint+1);
                path.push_back(str);
            }
            else // 不是回文串，跳过
                continue;
            backtracking(s, i+1);
            path.pop_back();
        }
    }

    vector<vector<string>> partition(string s) {
        backtracking(s, 0);
        return result;
    }
};
```

### 使用迭代器的方式插入和删除字符串中的字符（insert()、erase()）
+ 使用迭代器的方式插入和删除字符串中的字符:

```cpp
s.insert(s.begin()+i+1, '.'); // 在 i+1 位置插入字符
s.erase(s.begin()+i+1); // 在 i+1 位置删除字符
```

+ 示例题目：
	+ 复原 IP 地址：[93.复原IP地址](https://leetcode.cn/problems/restore-ip-addresses/description/)]( https://leetcode.cn/problems/palindrome-partitioning/description/ )
	+ 代码：

```cpp
class Solution {
public:
    // 判断子串中的数字是否合法，区间[start, end]
    bool isVaild(string s, int start, int end){
        int sum = 0;
        if(end-start>2)
            return false;
        for(int i=start; i<=end; i++){
            if(s[i]-'0'>9 || s[i]-'0'<0)
                return false;
            else{
                sum *= 10;
                sum += s[i]-'0';
            }
        }
        if(end-start == 0 && s[start]-'0' == 0)
            return true;
        else if(end-start > 0 && s[start]-'0' == 0)
            return false;
        else if(sum > 0 && sum <= 255)
            return true;
        else
            return false;
    }

    vector<string> result;

    void backtracking(string s, int startpoint, int pointSum){
        // 终止条件
        if(pointSum == 3){ // 已经插入了 3 个点
            // 判断从 startpoint 到末尾的子串是否合法
            if(isVaild(s, startpoint, s.size()-1)){ 
                result.push_back(s);
            }
            return;
        } 
        // 递归回溯逻辑
        for(int i=startpoint; i<s.size(); i++){
            if(isVaild(s, startpoint, i)){
                s.insert(s.begin()+i+1, '.');
                pointSum++;
                // i+2 跳过刚加上去的 .
                backtracking(s, i+2, pointSum);
                pointSum--;
                s.erase(s.begin()+i+1);
            }
            else
                continue;
        }
    }

    vector<string> restoreIpAddresses(string s) {
        if(s.size() < 4 || s.size() > 12) 
            return result; // 算是剪枝了
        backtracking(s, 0, 0);
        return result;
    }
};
```

## 哈希表相关
### set
+ 在 C++中，`set` 提供以下三种数据结构，其底层实现以及优劣如下表所示：

![set 的三种实现方式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250517202801.png)

+ `std::unordered_set` 底层实现为哈希表，`std::set` 和 `std::multiset` 的底层实现是红黑树，红黑树是一种平衡二叉搜索树，所以 `key` 值是有序的，但 `key` 不可以修改，改动 `key` 值会导致整棵树的错乱，所以只能删除和增加。
+ 当我们要使用集合来解决哈希问题的时候，优先使用 `unordered_set`，因为它的查询和增删效率是最优的，如果需要集合是有序的，那么就用 `set`，如果要求不仅有序还要有重复数据的话，那么就用 `multiset`。
+ 用法示例：
	+ 两个数组的交集：[349.两个数组的交集](https://leetcode.cn/problems/intersection-of-two-arrays/description/)
+ 代码：

```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> res;
        unordered_set<int> nums_1(nums1.begin(),nums1.end());
        for(int i=0; i<nums2.size(); i++)
        {
            if(nums_1.find(nums2[i])!=nums_1.end())
            {
                res.insert(nums2[i]);
            }
        }
        return vector<int>(res.begin(),res.end());
    }
};
```

+ 从这题可以学到两种用法：
	+ `find()`：找到的是对应数据位置的迭代器（迭代器：`set<int>::iterator`），找不到为 `nums_1.end()` 。（ `nums_1.begin()` 表示第一个元素，`nums_1.end()` 表示最后一个元素的后一位）。
	+ `unordered_set<int> nums_1(nums1.begin(), nums1.end());`：可以将一个容器的内容直接初始化到另外一个容器中，在末尾也有：`return vector<int>(res.begin(),res.end());`！

### map
+ 在 C++中，`map` 提供以下三种数据结构，其底层实现以及优劣如下表所示：

![map 的三种实现方式](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250518195557.png)

+ `std::unordered_map` 底层实现为哈希表，`std::map` 和 `std::multimap` 的底层实现是红黑树。
 + 用法示例：
	+ 两数之和：[1.两数之和](https://leetcode.cn/problems/two-sum/description/)
+ 代码：

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> mp;
        vector<int> ans;
        for(int i=0; i<nums.size(); i++)
        {
            if(mp.find(target-nums[i]) == mp.end())
            {
                mp.insert({nums[i],i});//注意map插入的形式
            }
            else
            {
                ans.push_back(i);
                unordered_map<int,int>::iterator it = mp.find(target-nums[i]);
                ans.push_back(it->second);
                break;
            }
        }
        return ans;
    }
};
```

+ 从这题可以学到两种用法：
	+ 注意 `map` 的插入用法，`mp.insert({nums[i],i});` 和 `mp.insert(pair<int, int>(nums[i], i));`，前者需要 `C++11` 或更高的版本，而后者适用于所有 `C++` 版本。
	+ 注意 `map` 的迭代器用法：其中 `unordered_map<int,int>::iterator` 也可以换成 `auto`，同样后者需要 `C++11` 或更高的版本，而前者适用于所有 `C++` 版本。

```cpp
unordered_map<int,int>::iterator it = mp.find(target-nums[i]);
ans.push_back(it->second);
```

## 栈和队列相关
### 双端队列（deque）
+ 在 C++中，`deque` 双端队列提供的操作有以下几种（`front` 表示操作队列前面，`back` 表示操作队列后面）
+ 用法示例：
	+ 滑动窗口最大值：[239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)中的单调队列实现
	+ 弹出数据：`que.pop_back()`、`que.pop_front()`
	+ 传入数据：`que.push_back(value)`、`que.push_front(value)`
	+ 查询数据：`que.back()`、`que.front()`
+ 代码：

```cpp
class MyQueue { //单调队列（从大到小）
public:
    deque<int> que; // 使用deque来实现单调队列
    // 每次弹出的时候，比较当前要弹出的数值是否等于队列出口元素的数值，如果相等则弹出。
    // 同时pop之前判断队列当前是否为空。
    void pop(int value) {
        if (!que.empty() && value == que.front()) {
            que.pop_front();
        }
    }
    // 如果push的数值大于入口元素的数值，那么就将队列后端的数值弹出，直到push的数值小于等于队列入口元素的数值为止。
    // 这样就保持了队列里的数值是单调从大到小的了。
    void push(int value) {
        while (!que.empty() && value > que.back()) {
            que.pop_back();
        }
        que.push_back(value);
    }
    // 查询当前队列里的最大值 直接返回队列前端也就是front就可以了。
    int front() {
        return que.front();
    }
};
```

