---
title: 左程云、代码随想录算法学习（一）
tags:
  - 算法学习
categories:
  - 算法学习
date: 2025-3-7 15:00:00
excerpt: C++、编程语言、算法学习
---
## 左程云、代码随想录算法学习（一）
## 二分法
+ 二分查找法：[704.二分查找](https://leetcode.cn/problems/binary-search/description/)
+ 代码如下：

```cpp
#include <cstdio>
#include <cstdlib>
#include <vector>
using namespace std;

class Solution {
    public:
        int search(vector<int>& nums, int target) {
            int left = 0;
            int right = nums.size()-1;
            int mid = left + (right-left)/2;
            int ans = -1;
            while(left<=right) //注意点1
            {
                mid = left + (right-left)/2; //注意点2
                if(nums[mid]==target)
                {
                    ans = mid;
                    break;
                }
                else if(nums[mid]>target)
                {
                    right = mid-1; //注意点3
                }
                else
                {
                    left = mid+1; //注意点4
                }
            }
            printf("%d\n",ans);
            return ans;
        }
    };

int main()
{
    Solution s;
    vector<int> v;
    for(int i=0;i<5;i++)
    {
        v.push_back(i);
    }
    s.search(v,0);

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ 变式 1：查找第一个和 `target` 相等的元素
+ 代码如下：

```cpp
//查找第一个和target相等的元素
int binary_search2(vector<int>& nums, int target) 
{
    int left = 0;
    int right = nums.size()-1;
    int mid,cmp;
    while(left<=right)
    {
        mid = left + (right - left)/2;
        cmp = target - nums[mid];
        if(cmp < 0) // target < arr[mid]
        {
            right = mid - 1;
        }
        else if(cmp > 0)
        {
            left = mid + 1;
        }
        else 
        {
            // 注意点：在[left,right]的区间找
            if(mid == left || nums[mid-1]<target)
                return mid;
            else
                right = mid - 1;
        }
    }
    return -1;
}
```

+ 变式 2：查找第一个大于等于 `target` 的元素
+ 代码如下：

```cpp
//查找第一个大于等于target的元素
int binary_search3(vector<int>& nums, int target) 
{
    int left = 0;
    int right = nums.size()-1;
    int mid,cmp;
    while(left<=right)
    {
        mid = left + (right - left)/2;
        cmp = target - nums[mid];
        if(cmp <= 0) // 注意点：target <= nums[mid]
        {
            if( mid == left || nums[mid - 1] < target)
                return mid;
            else
                right = mid - 1;
        }
        else
        {
            left = mid + 1;
        }
    }
    return -1;
}
```

+ 变式 3：查找最后一个小于等于 `target` 的值
+ 代码如下：

```cpp
//查找最后一个小于等于target的元素
int binary_search4(vector<int>& nums, int target) 
{
    int left = 0;
    int right = nums.size()-1;
    int mid,cmp;
    while(left<=right)
    {
        mid = left + (right - left)/2;
        cmp = target - nums[mid];
        if(cmp <= 0) // target <= nums[mid]
        {
            right = mid - 1;
        }
        else // 注意点
        {
            if(mid == right || nums[mid + 1]>target)
                return mid;
            else
                left = mid + 1;
        }
    }
    return -1;
}
```

## 异或运算
### 题目一
+ 如何不用额外变量交换两个数
+ 关键部分：

```cpp
//采用异或运算不用额外变量交换两个数
void yihuo_swap(int& a,int& b)
{
    a = a^b;
    b = a^b;
    a = a^b;
}
```

+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
using namespace std;

//采用异或运算不用额外变量交换两个数
void yihuo_swap(int& a,int& b)
{
    a = a^b;
    b = a^b;
    a = a^b;
}

int main()
{
    int num1 = 105;
    int num2 = 211;
    swap(num1,num2);
    printf("%d,%d",num1,num2);
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ 证明：
	+ 1、异或可以看成不进位二进制位加法；
	+ 2、具有 `a^a=0` 和 `a^0=a` 以及 `a^b=b^a` 的性质。
	+ 各个步骤详细推导如下：

![异或交换运算](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250307160112.png)
### 题目二

+ 一个数组中有一种数出现了奇数次，其他数都出现了偶数次，怎么找到并打印这种数？
+ 关键部分：

```cpp
//采用异或运算找到数组中出现奇数次的数
int yihuo_find(vector<int>& a)
{
    int eor = 0;
    for(int i=0;i<a.size();i++)
    {
        eor = eor ^ a[i];//用eor与数组中的所有元素相异或
    }
    return eor;
}
```

+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <vector>
using namespace std;

//采用异或运算找到数组中出现奇数次的数
int yihuo_find(vector<int>& a)
{
    int eor = 0;
    for(int i=0;i<a.size();i++)
    {
        eor = eor ^ a[i];//用eor与数组中的所有元素相异或
    }
    return eor;
}

int main()
{
    vector<int>  vec1= {1, 1, 3, 3, 3}; //3是奇数个
    int ans = yihuo_find(vec1);
    printf("%d",ans);
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ 证明：
	+ 1、具有 `a^a=0` 和 `a^0=a` 的性质；
	+ 2、偶数个数据累积异或后是 `0`，奇数个数据累积异或后就剩下它自身。

### 题目三
+ 怎么把一个 `int` 类型的数，提取出最右侧的 `1` 出来。
+ 关键部分：`ans=a&(-a)；`
+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <vector>
using namespace std;

//把int类型最右侧的1找出来
int yihuo_last_one(int& a)
{
    return a&(-a);//把int类型最右侧的1找出来
}

int main()
{
    int num = 28; 
    int ans = yihuo_last_one(num);
    printf("%d",ans);
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ 证明：

![题目三](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250307165903.png)

### 题目四
+ 一个数组中有两个不相等的数出现了奇数次，其他数都出现了偶数次，怎么找到并打印这两个数？
+ 关键代码：

```cpp
//采用异或运算找到数组中出现奇数次的两个数a、b
void yihuo_find(vector<int>& a,int& ans_1,int& ans_2)
{
    int eor = 0,eor_2 = 0;
    for(int i=0;i<a.size();i++)
    {
        eor = eor ^ a[i];//找到eor = a^b
    }
    //找到eor = a^b中的最右侧的1
    int OnlyOne = yihuo_last_one(eor);
    //由于eor = a^b中的最右侧为1，a和b在这一位一定不相等
    //只需要在数组中找到这一位只为1的数再做一次eor即可把a或者b找出来
    for(int i=0;i<a.size();i++)
    {
        if((OnlyOne & a[i]) != 0)
        {
            eor_2 = eor_2 ^ a[i];//找到eor_2 = a或者eor_2 = b
        }
    }
    ans_1 = eor_2;
    ans_2 = eor_2 ^ eor;
}
```

+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <vector>
using namespace std;

//把int类型最右侧的1找出来
int yihuo_last_one(int& a)
{
    return a&(-a);//把int类型最右侧的1找出来
}

//采用异或运算找到数组中出现奇数次的两个数a、b
void yihuo_find(vector<int>& a,int& ans_1,int& ans_2)
{
    int eor = 0,eor_2 = 0;
    for(int i=0;i<a.size();i++)
    {
        eor = eor ^ a[i];//找到eor = a^b
    }
    //找到eor = a^b中的最右侧的1
    int OnlyOne = yihuo_last_one(eor);
    //由于eor = a^b中的最右侧为1，a和b在这一位一定不相等
    //只需要在数组中找到这一位只为1的数再做一次eor即可把a或者b找出来
    for(int i=0;i<a.size();i++)
    {
        if((OnlyOne & a[i]) != 0)
        {
            eor_2 = eor_2 ^ a[i];//找到eor_2 = a或者eor_2 = b
        }
    }
    ans_1 = eor_2;
    ans_2 = eor_2 ^ eor;
}

int main()
{
    int ans_1=0,ans_2=0;
    vector<int>  vec1= {1, 1, 3, 3, 3, 6}; 
    yihuo_find(vec1,ans_1,ans_2);
    printf("%d、%d\n",ans_1,ans_2);
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

### 题目五
+ 一个数组中有一种数出现 `K` 次，其他数都出现了 `M` 次，`M>1`，`K<M`，请找到出现了 `K` 次的数，要求额外空间复杂度 `O(1)`，时间复杂度 `O(N)`。
+ 关键代码：

```cpp
//一个数组中有一种数出现K次，其他数都出现了M次，M>1，K<M，请找到出现了K次的数
int yihuo_find_KM(vector<int>& a,int& k,int& m)
{
    int arr[32];
    fill_n(arr,32,0);//创建全0数组
    for(int i=0;i<a.size();i++)
    {
        for(int j=0;j<32;j++)//一个int型有32位
        {
            arr[j]+=((a[i]>>j) & 1);//将这个数组中的每个位的1都相加上去
        }
    }
    int ans = 0;
    for(int k=0;k<32;k++)//最后判断每个位上对m取余是否等于0
    {
        if(arr[k]%m != 0)//对m取余不等于0，则在第k位上，有1
        {
            ans |= (1<<k);//将第k位的1或上去
        }
    }
    return ans;
}
```

+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

//一个数组中有一种数出现K次，其他数都出现了M次，M>1，K<M，请找到出现了K次的数
int yihuo_find_KM(vector<int>& a,int& k,int& m)
{
    int arr[32];
    fill_n(arr,32,0);//创建全0数组
    for(int i=0;i<a.size();i++)
    {
        for(int j=0;j<32;j++)//一个int型有32位
        {
            arr[j]+=((a[i]>>j) & 1);//将这个数组中的每个位的1都相加上去
        }
    }
    int ans = 0;
    for(int k=0;k<32;k++)//最后判断每个位上对m取余是否等于0
    {
        if(arr[k]%m != 0)//对m取余不等于0，则在第k位上，有1
        {
            ans |= (1<<k);//将第k位的1或上去
        }
    }
    return ans;
}

int main()
{
    int ans=0,k=2,m=3;
    vector<int>  vec1= {4, 4, 3, 3, 3, 6, 6, 6}; 
    ans = yihuo_find_KM(vec1,k,m);
    printf("%d\n",ans);
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

## 数组
### 题目一
+ 有序数组的平方：[977.有序数组的平方](https://leetcode.cn/problems/squares-of-a-sorted-array/description/)
+ 思路：
	+ 能够找到数组 `nums` 中负数与非负数的分界线，那么就可以用类似「**归并排序**」的方法。
	+ 具体地，设 `index` 为数组 `nums` 中负数与非负数的分界线，也就是说，`nums[0]` 到 `nums[index]` 均为负数，而 `nums[index+1]` 到 `nums[len−1]` 均为非负数。当我们将数组 `nums` 中的数平方后，那么 `nums[0]` 到 `nums[index]` 单调递减，`nums[index+1]` 到 `nums[len−1]` 单调递增。
	+ 由于我们得到了两个已经有序的子数组，因此就可以使用归并的方法进行排序了。具体地，使用两个指针分别指向位置 `index` 和 `index+1`，每次比较两个指针对应的数，选择较小的那个放入答案并移动指针。当某一指针移至边界时，将另一指针还未遍历到的数依次放入答案。
+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <vector>
#include <cmath>
using namespace std;

class Solution
{
public:
    vector<int> sortedSquares(vector<int> &nums)
    {
        int len = nums.size();
        if (len == 1)
        {
            nums[0] = nums[0] * nums[0];
            return nums;
        }
        int index = -1;
        for (int i = 0; i < len; i++)
        {
            if (nums[i] < 0)
                index = i;
            else
                break;
        }
        int left = index;
        int right = index + 1;
        vector<int> arr;
        while (left >= 0 && right <= len - 1)
        {
            if (nums[left] * nums[left] <= nums[right] * nums[right])
            {
                arr.push_back(nums[left] * nums[left]);
                left--;
            }
            else
            {
                arr.push_back(nums[right] * nums[right]);
                right++;
            }
        }
        while (left >= 0)
        {
            arr.push_back(nums[left] * nums[left]);
            left--;
        }
        while (right <= len - 1)
        {
            arr.push_back(nums[right] * nums[right]);
            right++;
        }
        return arr;
    }
};

int main()
{
    Solution mysolution;
    vector<int> vec = {0, 2};
    vector<int> ans;
    int num;
    ans = mysolution.sortedSquares(vec);
    num = ans.size();
    for (int i = 0; i < num; i++)
    {
        printf("%d\n", ans[i]);
    }

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

### 题目二
+ 长度最小的子数组：[209.长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/description/)
+ 思路：
	+ 滑动窗口就是满足总和 `sum` 大于等于 `target` 的长度最小的连续子数组。
	+ 滑动窗口的起始位置如何移动：如果当前窗口的总和 `sum` 大于等于 `target` 了，窗口就要向前移动了（也就是该缩小 `i++` 了）。
	+ 滑动窗口的结束位置如何移动：窗口的结束位置就是遍历数组的指针，也就是 `for` 循环里的索引，`j++` 。
	+ 可以发现滑动窗口的精妙之处在于根据当前子序列和大小的情况，不断调节子序列的起始位置。从而将 $O(n^2)$ 暴力解法降为 $O(n)$ 。
+ 代码：

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int len = nums.size(); //滑动窗口的长度
        int sum = 0; // 滑动窗口数值之和
        int i = 0; //滑动窗口的起始位置
        //j表示移动窗口结束的位置
        for(int j=0;j<nums.size();j++)
        {
            sum+=nums[j];
            while(sum>=target) //直到滑动窗口中的数值之和小于target
            {
                sum-=nums[i];
                len = min(len,j-i+1);//取长度中的最小值
                i++;
            }
        }
        if(i==0 && sum<target)
            return 0;
        else
            return len;
    }
};
```

### 题目三
+ 螺旋矩阵 II：[59.螺旋矩阵 II](https://leetcode.cn/problems/spiral-matrix-ii/description/)
+ 思路：
	+ 这道题就是模拟螺旋矩阵生成的过程，分为四个方向进行循环，同时设定相应的循环边界条件达到模拟的实现。
	+ 注意在 C++ 代码中 `vector` 的二维动态数组可以定义为：`vector<vector<int>> mat(n,vector<int>(n));`
+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <vector>
#include <cmath>
using namespace std;

class Solution {
    public:
        vector<vector<int>> generateMatrix(int n) {
            //创建二维数组和下标
            vector<vector<int>> mat(n,vector<int>(n));
            int i=0;
            int j=0;
            //模拟部分
            int count = 1;
            int step = n;
            int num = 1;
            while(step>0)
            {
                //向右横着
                for(int k=0;k<step;k++)
                {
                    //printf("i=%d j=%d num = %d\n",i,j,num);
                    mat[i][j++] = num++;
                }
                count++;
                if(count%2 == 0)
                    step--;
                i++;
                j--;
                //向下竖着
                for(int k=0;k<step;k++)
                {
                    //printf("i=%d j=%d num = %d\n",i,j,num);
                    mat[i++][j] = num++;
                }
                count++;
                if(count%2 == 0)
                    step--;
                j--;
                i--;
                //向左横着
                for(int k=0;k<step;k++)
                {
                    //printf("i=%d j=%d num = %d\n",i,j,num);
                    mat[i][j--] = num++;
                }
                count++;
                if(count%2 == 0)
                    step--;
                i--;
                j++;
                //向上竖着
                for(int k=0;k<step;k++)
                {
                    //printf("i=%d j=%d num = %d\n",i,j,num);
                    mat[i--][j] = num++;
                }
                count++;
                if(count%2 == 0)
                    step--;
                j++;
                i++;
            }
            return mat;
        }
    };

int main()
{
    Solution mysolution;
    vector<vector<int>> ans;
    int num = 4;
    ans = mysolution.generateMatrix(num);
    for (int i = 0; i < num; i++)
    {
        for(int j = 0;j < num; j++)
        {
            printf("%d\n", ans[i][j]);
        }
    }

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

### 题目四
+ 区间和：[58. 区间和（第九期模拟笔试）](https://kamacoder.com/problempage.php?pid=1070)
	+ 本题主要用于熟悉 ACM 模式的输入输出！
+ 代码如下：

```cpp
#include <cstdio>
#include <algorithm>
#include <vector>

using namespace std;

int main(void)
{
    int n,num,sum;
    int pre,last;
    scanf("%d",&n);
    vector<int> vec;
    for(int i=0;i<n;i++)
    {
        scanf("%d",&num);
        vec.push_back(num);
    }
    //循环输入输出部分
    while(scanf("%d %d",&pre,&last)!=EOF)
    {
        sum = 0;
        for(int i=pre;i<=last;i++)
        {
            sum+=vec[i];
        }
        printf("%d\n",sum);
    }
    return 0;
}
```

### 题目五
+ 开发商购买土地：[44. 开发商购买土地（第五期模拟笔试）](https://kamacoder.com/problempage.php?pid=1044)
+ 思路：
	+ 前缀和计算
		+ **横向前缀和**：计算每一行从左到右的累加和，存储在 `heng` 数组中；
		+ **竖向前缀和**：计算每一列从上到下的累加和，存储在 `shu` 数组中。
	+ 寻找最小差值：
		+ **竖向分割**：尝试所有可能的竖向分割线位置，计算左右两部分和的差值，差值计算公式为 `shu[m-1] - 2*shu[i]`（总列和减去两倍左侧和）；
		+ **横向分割**：尝试所有可能的横向分割线位置，计算上下两部分和的差值，差值计算公式为 `heng[n-1] - 2*heng[i]`（总行和减去两倍上方和）；
		+ 记录所有可能分割中的最小绝对差值。
+ 代码：

```cpp
#include <cstdio>
#include <vector>
#include <limits>
#include <cmath>

using namespace std;

int main(void)
{
    int n,m;
    scanf("%d %d",&n,&m);
    //创建二维数组
    vector<vector<int>> mat(n,vector<int>(m));
    //创建竖向前缀和数组和变量
    vector<int> shu(m);
    int sum_shu = 0;
    //创建横向前缀和数组和变量
    vector<int> heng(n);
    int sum_heng = 0;
    for(int i=0;i<n;i++)
    {
        for(int j=0;j<m;j++)
        {
            scanf("%d",&mat[i][j]);
            sum_heng += mat[i][j];
            heng[i] = sum_heng;
        }
    }
    for(int i=0;i<m;i++)
    {
        for(int j=0;j<n;j++)
        {
            sum_shu += mat[j][i];
            shu[i] = sum_shu;
        }
    }
    //定义最小值
    int min_num = numeric_limits<int>::max();
    //比较竖向分割
    int num = 0;
    for(int i=0;i<m;i++)
    {
        num = shu[m-1]-2*shu[i];
        min_num = min(min_num,abs(num));
    }
    //比较横向分割
    for(int i=0;i<n;i++)
    {
        num = heng[n-1]-2*heng[i];
        min_num = min(min_num,abs(num));
    }

    printf("%d",min_num);

    return 0;
}
```

## 链表
### 题目一
+ 移除链表元素：[203.移除链表元素](https://leetcode.cn/problems/remove-linked-list-elements/description/)
+ 直接解法代码:

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
using namespace std;

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

//链表结构
struct ListNode 
{
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    //构造函数
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};

//解决方案1
class Solution {
    public:
        ListNode* removeElements(ListNode* head, int val) {
            ListNode *now = head;
            ListNode *pre = nullptr;//now的前一个
            ListNode *temp = nullptr;
            while(now != nullptr)
            {
                //头节点就是要删除的数据
                if(head->val == val)
                {
                    head = head->next;
                    delete now;
                    now = head;
                }
                //头节点不是要删除的数据
                else if(now->val == val && head->val != val)
                {
                    pre->next = now->next;
                    temp = now;
                    now = now->next;
                    delete temp;
                    temp = nullptr;
                }
                //没有要删除的数据
                else
                {
                    pre = now;
                    now = now->next;
                }
            }
            return head;
        }
    };

int main()
{
    ListNode *head = new ListNode(1);
    ListNode *now = head;
    for(int i=2;i<=8;i++)
    {
        ListNode *temp = new ListNode(i);
        now->next = temp;
        now = now->next;
    }
    now = head;
    
    Solution s;
    now = s.removeElements(head,1);

    while(now->next!=nullptr)
    {
        printf("%d\n",now->val);
        now = now->next;
    }
    
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ 虚拟头节点解法代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
using namespace std;

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

//链表结构
struct ListNode 
{
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    //构造函数
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};

//解决方案
class Solution {
    public:
        //虚拟头节点解法
        ListNode* removeElements(ListNode* head, int val) {
            ListNode* vitHead = new ListNode(-1);
            vitHead->next = head;
            ListNode* now = vitHead;
            while(now->next != nullptr)
            {
                if(now->next->val == val)
                {
                    ListNode* temp = now->next; 
                    now->next = now->next->next;
                    now = now->next;
                    delete temp;
                }
                else
                {
                    now = now->next;
                }
            }
            return vitHead->next;
        }
    };

int main()
{
    ListNode *head = new ListNode(1);
    ListNode *now = head;
    for(int i=2;i<=8;i++)
    {
        ListNode *temp = new ListNode(i);
        now->next = temp;
        now = now->next;
    }
    now = head;
    
    Solution s;
    now = s.removeElements(head,1);

    while(now->next!=nullptr)
    {
        printf("%d\n",now->val);
        now = now->next;
    }
    
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

### 题目二
+ 移除链表元素：[707.设计链表](https://leetcode.cn/problems/design-linked-list/description/)
+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
using namespace std;

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

 //解决方案
 class MyLinkedList {
    public:

        //链表结构
        struct ListNode 
        {
            int val;
            ListNode *next;
            ListNode() : val(0), next(nullptr) {}
            //构造函数
            ListNode(int x) : val(x), next(nullptr) {}
            ListNode(int x, ListNode *next) : val(x), next(next) {}
        };

        ListNode* _VitHead;
        int _size = -1;
    
        MyLinkedList() {
            _VitHead = new ListNode(-1);
            _size = -1;
        }
        
        int get(int index) {
            if((_size < 0) || (index > _size))
            {
                return -1;
            }
            else
            {
                ListNode* now = _VitHead;
                for(int i=0;i<=index;i++)
                {
                    now = now->next;
                }
                return now->val;
            }
        }
        
        void addAtHead(int val) {
            ListNode* temp = new ListNode(val);
            temp->next = _VitHead->next;
            _VitHead->next = temp;
            _size++;
        }
        
        void addAtTail(int val) {
            ListNode* temp = new ListNode(val);
            ListNode* now = _VitHead;
            while(now->next!=nullptr)
            {
                now = now->next;
            }
            now->next = temp;
            _size++;
        }
        
        void addAtIndex(int index, int val) {
            ListNode* temp = new ListNode(val);
            ListNode* now = _VitHead;
            ListNode* pre = _VitHead;
            if(index > _size+1)
                return;
            for(int i=0;i<=index;i++)
            {
                
                pre = now;
                now = now->next;
            }
            temp->next = pre->next;
            pre->next = temp;
            _size++;
        }
        
        void deleteAtIndex(int index) {
            ListNode* now = _VitHead;
            ListNode* pre = _VitHead;
            if(index > _size)
                return;
            for(int i=0;i<=index;i++)
            {
                pre = now;
                now = now->next;
            }
            pre->next = now->next;
            delete now;
            _size--;
        }
    };
    
/**
    * Your MyLinkedList object will be instantiated and called as such:
    * MyLinkedList* obj = new MyLinkedList();
    * int param_1 = obj->get(index);
    * obj->addAtHead(val);
    * obj->addAtTail(val);
    * obj->addAtIndex(index,val);
    * obj->deleteAtIndex(index);
*/

int main()
{
    MyLinkedList* myLinkedList = new MyLinkedList();
    myLinkedList->addAtHead(7);
    myLinkedList->addAtHead(2);
    myLinkedList->addAtHead(1);
    myLinkedList->addAtIndex(3, 0);
    myLinkedList->deleteAtIndex(2);
    myLinkedList->addAtHead(6);
    myLinkedList->addAtTail(4);
    cout << myLinkedList->get(4) << endl; 
    
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

### 题目三
+ 反转链表：[206.反转链表](https://leetcode.cn/problems/reverse-linked-list/description/)
+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
using namespace std;

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

//链表结构
struct ListNode 
{
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    //构造函数
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};

//解决方案
class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            ListNode* now;
            ListNode* pre;
            ListNode* temp;
            pre = head;
            now = head;
            if((now == nullptr) || (now->next == nullptr))
                return head;
            now = pre->next;
            pre->next = nullptr;
            while(now != nullptr)
            {
                temp = now->next;
                now->next = pre;
                pre = now;
                now = temp;
            }
            return pre;
        }
    };

int main()
{
    ListNode *head = new ListNode(1);
    ListNode *now = head;
    for(int i=2;i<=8;i++)
    {
        ListNode *temp = new ListNode(i);
        now->next = temp;
        now = now->next;
    }
    now = head;
    
    Solution s;
    now = s.reverseList(head);

    while(now->next!=nullptr)
    {
        printf("%d\n",now->val);
        now = now->next;
    }
    
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

### 题目四
+ 两两交换链表中的节点：[24.两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/description/)
+ 代码：普通解法

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
using namespace std;

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

//链表结构
struct ListNode 
{
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    //构造函数
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};

//解决方案
class Solution {
    public:
        ListNode* swapPairs(ListNode* head) {
            ListNode* temp = nullptr;
            ListNode* temp1 = nullptr;
            ListNode* now = nullptr;
            int count = 0;
            if((head==nullptr) || (head->next==nullptr))
                return head;
            else if(head->next->next == nullptr)
            {
                temp = head->next;
                head->next = nullptr;
                temp->next = head;
                return temp;
            }
            else
            {
                now = head;
                int count = 0;
                while(now->next!=nullptr && now->next->next!=nullptr)
                {
                    if(count==0)
                    {
                        head = now->next;
                        now->next = now->next->next;
                        head->next = now;
                        count++;
                    }
                    else if(count%2==1)
                    {
                        temp = now->next;
                        temp1 = now->next->next->next;
                        now->next = now->next->next;
                        now->next->next = temp;
                        temp->next = temp1;
                        count++;
                        now = now->next;
                    }
                    else
                    {
                        count++;
                        now = now->next;
                    }
                }
                return head;
            }
        }
    };

int main()
{
    ListNode *head = new ListNode(1);
    ListNode *now = head;
    for(int i=2;i<=7;i++)
    {
        ListNode *temp = new ListNode(i);
        now->next = temp;
        now = now->next;
    }
    now = head;
    
    Solution s;
    now = s.swapPairs(head);

    while(now!=nullptr)
    {
        printf("%d\n",now->val);
        now = now->next;
    }
    
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

+ 虚拟头节点解法：

```cpp
//虚拟头节点解法
class Solution {
    public:
        ListNode* swapPairs(ListNode* head) {
            ListNode* temp = nullptr;
            ListNode* temp1 = nullptr;
            ListNode* VitHead = new ListNode(-1);
            VitHead->next = head;
            ListNode* now = VitHead;
            while((now->next != nullptr) && (now->next->next != nullptr))
            {
                temp = now->next;
                temp1 = now->next->next->next;
                
                now->next = now->next->next;
                now->next->next = temp;
                temp->next = temp1;

                now = now->next->next;
            }
            return VitHead->next;
        }
    };
```

### 题目五
+ 删除链表的倒数第 `N` 个节点：[19.删除链表的倒数第 N 个节点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/description/)
+ 思路：使用双指针的方法，在 `now` 指针往后移动的同时使得 `pre` 指针与 `now` 指针保持 `N` 的距离，当 `now` 指针遍历到链表末尾后就可以采用 `pre` 指针来删除链表的倒数第 `N` 个节点。
+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
using namespace std;

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

//链表结构
struct ListNode 
{
    int val;
    ListNode *next;
    ListNode() : val(0), next(nullptr) {}
    //构造函数
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode *next) : val(x), next(next) {}
};

//解决方案
//虚拟头节点解法
class Solution {
    public:
        ListNode* removeNthFromEnd(ListNode* head, int n) {
            
            //创建虚拟头节点
            ListNode* VitHead = new ListNode(-1);
            VitHead->next = head;
            
            ListNode* now = VitHead;
            ListNode* pre = VitHead;
            int count = 0;

            while(now->next != nullptr)
            {
                now = now->next;
                count++;
                if(count > n)
                    pre = pre->next;
            }

            ListNode* temp = pre->next;
            pre->next = temp->next;
            delete temp;
            
            return VitHead->next;
        }
    };

int main()
{
    ListNode *head = new ListNode(1);
    ListNode *now = head;
    for(int i=2;i<=3;i++)
    {
        ListNode *temp = new ListNode(i);
        now->next = temp;
        now = now->next;
    }
    now = head;
    
    Solution s;
    now = s.removeNthFromEnd(head, 1);

    while(now!=nullptr)
    {
        printf("%d\n",now->val);
        now = now->next;
    }
    
    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

### 题目六
+  相交链表：[160.相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/description/)
+ 思路：

![相交链表思路](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250407170603.png)

+ 代码：

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
        ListNode *p = headA;
        ListNode *q = headB;
        while(q!=p)
        {
            if(p!=nullptr)
                p = p->next;
            else
                p = headB;
            if(q!=nullptr)
                q = q->next;
            else
                q = headA;
        }
        return p;
    }
};
```

### 题目七
+ 环形链表 II：[142.环形链表II](https://leetcode.cn/problems/linked-list-cycle-ii/description/)
+ 思路：[代码随想录-142.环形链表](https://programmercarl.com/0142.%E7%8E%AF%E5%BD%A2%E9%93%BE%E8%A1%A8II.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
+ 代码：

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
        ListNode* fast_ptr = head;
        ListNode* slow_ptr = head;
        while(fast_ptr != nullptr && fast_ptr->next != nullptr)
        {
            fast_ptr = fast_ptr->next->next;
            slow_ptr = slow_ptr->next;
            if(slow_ptr == fast_ptr)
            {
                ListNode* index1 = head;
                ListNode* index2 = fast_ptr;
                while(index1 != index2)
                {
                    index1 = index1->next;
                    index2 = index2->next;
                }
                return index1;
            }
        }
        return nullptr;
    }
};
```

## 哈希表
### 题目一
+ 四数相加 II：[454. 四数相加 II](https://leetcode.cn/problems/4sum-ii/description/)
+ 思路：
	1. 哈希表的构建：通过双层循环遍历 `nums1` 和 `nums2` ，计算所有可能的和 `a + b`，并使用哈希表 ` unordered_map<int, int> mp1;` 记录每个和出现的次数（key：`a + b`，value：`a + b` 出现的次数）。
	2. 补数查找：遍历 `nums3` 和 `nums4`，计算每对元素的和 `c + d`，并查找其相反数 `-(c + d)` 是否存在于哈希表中。若存在，则说明找到了一组满足条件的四元组，累加对应的次数到结果 `ans` 中。
+ 代码：

```cpp
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) 
    {
        unordered_map<int,int> mp1;
        for(int i=0; i<nums1.size(); i++)
        {
            for(int j=0; j<nums2.size(); j++){
                unordered_map<int,int>::iterator it = mp1.find(nums1[i]+nums2[j]);
                //没找到
                if(it == mp1.end()){
                    mp1.insert({nums1[i]+nums2[j],1});
                }
                //找到了
                else{
                    it->second++;
                }
            }
        }  
        int ans = 0;
        for(int i=0; i<nums3.size(); i++)
        {
            for(int j=0; j<nums4.size(); j++){
                unordered_map<int,int>::iterator it = mp1.find(0-nums3[i]-nums4[j]);
                if(it != mp1.end()){
                    ans += it->second; 
                }
            }
        }
        return ans;
    }
};
```

### 题目二
+ 三数之和：[15. 三数之和](https://leetcode.cn/problems/3sum/description/)
+ 思路：
	1. 这道题目与上一**两数之和**不同之处在于采用**双指针法**进行处理。
	2. 排序预处理：将数组排序，使相同的元素相邻，便于后续跳过重复元素，同时为双指针法创造条件。
	3. 遍历固定第一个数（`a`）：遍历数组，固定当前元素作为三元组的第一个数 `a`。若 `a > 0`，由于数组已排序，后续元素无法使三数之和为 `0`，直接返回结果。
	4. 去重处理（`a` 的去重）：若当前 `a` 与前一个元素相同（`i > 0 && nums[i] == nums[i-1]`），跳过该元素，避免重复的三元组。
	5. 双指针寻找后两个数（`b`, `c`）：
		+ 定义左右指针 `left` 和 `right`，初始分别指向 `i+1` 和数组末尾。
		+ 计算三数之和 `temp`：
			+ 若 `temp > 0`：右指针左移（减小总和）。
			+ 若 `temp < 0`：左指针右移（增大总和）。
			+ 若 `temp == 0`：找到有效三元组，加入结果，并对 `b` 和 `c` 去重。
	6. 去重处理（`b` 和 `c` 的去重）：找到有效三元组后，跳过所有与当前 `b`（`nums[left]`）和 `c`（`nums[right]`）相同的元素，避免重复。
+ 代码：

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        sort(nums.begin(),nums.end());
        for(int i=0; i<nums.size()-1; i++){
            if(nums[i]>0)
				break;
            //对a进行去重
            if(i>0 && nums[i] == nums[i-1])
                continue;
            //定义左右指针
            int left = i+1;
            int right = nums.size()-1;
            while(left < right){
                int temp = nums[i]+nums[left]+nums[right];
                if(temp>0) //偏大
                    right--;
                else if(temp<0) //偏小
                    left++;
                else{ //找到结果
                    result.push_back({nums[i],nums[left],nums[right]});
                    //对a和b去重
                    while(left < right && nums[left] == nums[left+1])
                        left++;
                    while(left < right && nums[right] == nums[right-1])
                        right--;
                    //去重后指向的数值与加进去的结果一样，需要左右指针再移动一次
                    left++;
                    right--;
                } 
            }
        }
        return result;
    }
};
```

### 题目三
+ 四数之和：[18. 四数之和](https://leetcode.cn/problems/4sum/)
+ 思路：
	+ 与前一题的三数之和类似，主要差距仅在外层再加一次循环。
	+ 需要注意一下**一级提前终止**（ `nums[i]>target && nums[i]>0` ）和**二级提前终止**（ `twoSum > target && twoSum > 0` ）的条件！
+ 代码：

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> result;
        sort(nums.begin(),nums.end());
        //定义左右指针和临时变量
        int left,right;
        long temp,twoSum;
        for(int i=0; i<nums.size()-1; i++){
	        //一级提前终止
            if(nums[i]>target && nums[i]>0)
                break;
            //去重
            if(i>0 && nums[i] == nums[i-1])
                continue;
            for(int j=i+1; j<nums.size(); j++){
                //二级提前终止
                twoSum = (long) nums[i] + nums[j];
                if (twoSum > target && twoSum > 0) 
                    break;
                //去重
                if(j>i+1 && nums[j] == nums[j-1])
                    continue;
                left = j+1;
                right = nums.size()-1;
                while(left < right){
                    temp = (long) nums[i] + (long) nums[j] + (long) nums[left] + (long) nums[right];
                    if(temp>target)
                        right--;
                    else if(temp<target)
                        left++;
                    else{
                        result.push_back({nums[i],nums[j],nums[left],nums[right]});
                        //去重
                        while(left<right && nums[left] == nums[left+1])
                            left++;
                        while(left<right && nums[right] == nums[right-1])
                            right--;
                        left++;
                        right--;
                    }
                }
            }
        }
        return result;
    }
};
```

## 字符串
### 题目一
+ 替换数字：[54. 替换数字（第八期模拟笔试）](https://kamacoder.com/problempage.php?pid=1064)
+ 思路：首先扩充数组到每个数字字符替换成 `number` 之后的大小，再从后向前进行填充。**其实很多数组填充类的问题，其做法都是先预先给数组扩容带填充后的大小，然后在从后向前进行操作。**
	+ 好处如下：
		1. 不用申请新数组。
		2. 从后向前填充元素，避免了从前向后填充元素时，每次添加元素都要将添加元素之后的所有元素向后移动的问题。
+ 代码：

```cpp
#include <cstdio>
#include <string>
#include <iostream>

using namespace std;

int main(void)
{
    string s;
    cin >> s;
    int count = 0;
    int num = s.size();
    for(int i=0; i<num; i++)
    {
        if(s[i]>='0' && s[i]<='9')
        {
            count++;
        }
    }
    //扩容
    s.resize(s.size() + count * 5);
    //从后往前调整字符串数组
    int j = s.size()-1;
    for(int i=num-1; i>=0; i--)
    {
        if(!(s[i]>='0' && s[i]<='9'))
        {
            s[j] = s[i];
            j--;
        }
        else
        {
            s[j--] = 'r';
            s[j--] = 'e';
            s[j--] = 'b';
            s[j--] = 'm';
            s[j--] = 'u';
            s[j--] = 'n';
        }
    }
    cout << s;
    return 0;
}
```

### 题目二
+ 反转字符串中的单词：[151.反转字符串中的单词](https://leetcode.cn/problems/reverse-words-in-a-string/description/)
+ 思路：
	+ 移除单词中多余的空格->整体反转字符串->逐个单词反转
	+ 这道题目需要注意的是自己写 `string deleteExtraSpaces(string s)` 函数代码来移除单词中多余的空格。
+ 代码：

```cpp
class Solution {
public:
    //删除单词之间多余的空格
    string deleteExtraSpaces(string s)
    {
        int slow = 0;
        for(int fast=0; fast<s.size(); fast++)
        {
            //不等于空格就处理
            if(s[fast]!=' ')
            {
                //补上空格
                if(slow!=0)
                    s[slow++] = ' ';
                //把单词填上去
                while(s[fast]!=' ' && fast<s.size())
                    s[slow++] = s[fast++];
            }
        }
        s.resize(slow);
        return s;
    }

    string reverseWords(string s) {
        s = deleteExtraSpaces(s);
        //将字符串整体翻转
        int i=0;
        int j=s.size()-1;
        while(i<j)
        {
            swap(s[i],s[j]);
            i++;
            j--;
        }
        //逐个将单词翻转
        int m,n,record=0;
        for(int i=0; i<=s.size(); i++)
        {
            if(s[i]==' ' || i==s.size())
            {
                m = record;
                record = i+1;
                n = i-1;
                while(m<n)
                {
                    swap(s[m],s[n]);
                    m++;
                    n--;
                }
            }
        }
        return s;
    }
};
```

### KMP 算法
+ 字符串的前缀：不包含最后一个字符的所有以第一个字符开头的连续子串。
+ 字符串的后缀：不包含第一个字符的所有以最后一个字符结尾的连续子串。
	+ 举例：字符串 `"aab"`
		+ 其前缀有：`“a”` 、`"aa"`
		+  其后缀有：`“b”` 、`"ab"`
+ 前缀表：前缀表要求的就是相同前后缀的长度。
	+ 字符串 `"aab"` 的最长**公共前后缀**的长度为 1。
	+ 前缀 `"aa"` 和后缀 `"ab"` 重合的部分。
+ KMP 算法在的 `next[]` 数组储存的就是前缀表。
	+ 前缀表是用来回退的，它记录了模式串与主串 (文本串) 不匹配的时候，模式串应该从哪里开始重新匹配。
+ `next[]` 数组的计算方式：
	+ 初始化：注意初始化 `i=1`
	+ 不相等的清空：使用 `while()` 循环持续回退
	+ 相等的情况
+ 代码：

```cpp
void getNext(vector<int> &next, string s)
    {
        // 初始化
        int j = 0;
        next[j] = 0;
        for (int i = 1; i < s.size(); i++) //注意初始化 i=1
        {
            // 不相等的情况
            while (j > 0 && s[i] != s[j])
            {
                // 回退
                j = next[j - 1];
            }
            // 相等的情况
            if (s[i] == s[j])
            {
                j++;
            }
            next[i] = j;
        }
    }
```

### 题目三
+ 找出字符串中第一个匹配的下标：[28.找出字符串中第一个匹配的下标](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/description/)
+ 思路：KMP 算法的最重要应用，使用前缀表 `next[]` 数组当出现字符串不匹配时，可以记录一部分之前已经匹配的文本内容，利用这些信息避免从头再去做匹配。
+ 代码：

```cpp
class Solution
{
public:
    void getNext(vector<int> &next, string s)
    {
        // 初始化
        int j = 0;
        next[j] = 0;
        for (int i = 1; i < s.size(); i++)
        {
            // 不相等的情况
            while (j > 0 && s[i] != s[j])
            {
                // 回退
                j = next[j - 1];
            }
            // 相等的情况
            if (s[i] == s[j])
            {
                j++;
            }
            next[i] = j;
        }
    }

    int strStr(string haystack, string needle)
    {
        vector<int> next(needle.size());
        getNext(next, needle);
        int j = 0;
        for (int i = 0; i < haystack.size(); i++)
        {
            // 不相等
            while (j > 0 && haystack[i] != needle[j])
            {
                j = next[j - 1];
            }
            // 相等但不是末尾,i和j都向后移动
            if (haystack[i] == needle[j] && (j != needle.size() - 1))
            {
                j++;
            }
            // 相等而且是末尾
            else if (haystack[i] == needle[j] && (j == needle.size() - 1))
            {
                return (i - needle.size() + 1);
            }
        }
        return -1;
    }
};
```

### 题目四
+ 重复的子字符串：[459.重复的子字符串](https://leetcode.cn/problems/repeated-substring-pattern/description/)
+ 思路：
	+ 如果 `s.size() % (s.size()-next[s.size()-1]) == 0` ，则说明数组的长度正好可以被最长相等前后缀不包含的子串的长度整除，说明该字符串有重复的子字符串。
	+ 具体思路和证明：[459.重复的子字符串思路](https://programmercarl.com/0459.%E9%87%8D%E5%A4%8D%E7%9A%84%E5%AD%90%E5%AD%97%E7%AC%A6%E4%B8%B2.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
+ 代码：

```cpp
class Solution {
public:
    void getNext(vector<int>& next,string s)
    {
        //初始化
        int j=0;
        next[j] = 0;
        for(int i=1; i<s.size(); i++)
        {
            //如果不相等，回退
            while(j>0 && s[i]!=s[j])
            {
                j = next[j-1];//回退
            }
            //如果相等，则i和j都增加
            if(s[i]==s[j])
            {
                j++;
            }
            next[i] = j;
        }
    }
    
    bool repeatedSubstringPattern(string s) {
        vector<int> next(s.size());
        getNext(next,s);
        int ans = s.size() % (s.size()-next[s.size()-1]);
        if(ans || next[s.size()-1]==0)
            return false;
        else
            return true;
    }
};
```

## 队列和栈
### 题目一
+ 用栈实现队列：[232.用栈实现队列](https://leetcode.cn/problems/implement-queue-using-stacks/description/)
+ 思路：[代码随想录-232.用栈实现队列](https://programmercarl.com/0232.%E7%94%A8%E6%A0%88%E5%AE%9E%E7%8E%B0%E9%98%9F%E5%88%97.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <stack>
using namespace std;

class MyQueue {
    public:
        stack<int> In;
        stack<int> Out;
        MyQueue() {}
    
        void push(int x) { In.push(x); }
    
        int pop() {
            if (!Out.empty()) {
                int temp = Out.top();
                Out.pop();
                return temp;
            } else {
                while (!In.empty()) {
                    Out.push(In.top());
                    In.pop();
                }
                return Out.top();
            }
        }
    
        int peek() {
            if (!Out.empty())
                return Out.top();
            else {
                while (!In.empty()) {
                    Out.push(In.top());
                    In.pop();
                }
                return Out.top();
            }
        }
    
        bool empty() {
            if (In.empty() && Out.empty())
                return true;
            else
                return false;
        }
    };
    
    /**
     * Your MyQueue object will be instantiated and called as such:
     * MyQueue* obj = new MyQueue();
     * obj->push(x);
     * int param_2 = obj->pop();
     * int param_3 = obj->peek();
     * bool param_4 = obj->empty();
     */
    
int main()
{
    MyQueue* myQueue = new MyQueue();
    //myQueue->push(1);
    myQueue->push(2);
    printf("%d\n",myQueue->peek());
    myQueue->pop();
    //myQueue->pop();
    printf("%d\n",myQueue->empty());
    //myQueue->peek();

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

### 题目二
+ 用队列实现栈：[225.用队列实现栈](https://leetcode.cn/problems/implement-stack-using-queues/description/)
+ 思路：采用两个队列，始终保持一个队列为空，当要 `pop` 数据的时候，把非空队列的元素依次输出到另外一个空队列，剩下最后一个元素进行返回和 `pop`。`push` 数据时要向非空队列传入数据，若两个队列都是空的就向任意一个队列传数据即可。
+ 代码：

```cpp
class MyStack {
public:
    queue<int> qu_1;
    queue<int> qu_2;
    MyStack() {}

    void push(int x) 
    {
        if(!qu_1.empty())
            qu_1.push(x);
        else if(!qu_2.empty())
            qu_2.push(x);
        else
            qu_1.push(x);
    }

    int pop() 
    {
        int temp;
        if(!qu_1.empty())
        {
            while(qu_1.size()>1)
            {
                qu_2.push(qu_1.front());
                qu_1.pop();
            }
            temp = qu_1.front();
            qu_1.pop();
            return temp;
        }
        else
        {
            while(qu_2.size()>1)
            {
                qu_1.push(qu_2.front());
                qu_2.pop();
            }
            temp = qu_2.front();
            qu_2.pop();
            return temp;
        }
    }

    int top() 
    {
        if(!qu_1.empty())
            return qu_1.back();
        else
            return qu_2.back();
    }

    bool empty() 
    {
        if(qu_1.empty() && qu_2.empty())
            return true;
        else
            return false;
    }
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```

### 题目三
+ 删除字符串中的所有相邻重复项：[1047. 删除字符串中的所有相邻重复项](https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string/description/)
+ 思路：
	1. **初始化一个空栈**：用于存储字符。
	2. **遍历字符串**：
		+ 如果栈为空，或者当前字符与栈顶字符不同，则将当前字符压入栈。 
		+ 如果当前字符与栈顶字符相同，则弹出栈顶字符（表示删除这对相邻重复字符）。
	3. **构建结果字符串**：
		+ 遍历结束后，栈中剩下的字符就是删除所有相邻重复字符后的结果。
		+ 由于栈是后进先出的，我们需要将栈中的字符**逆序**才能得到正确的顺序。
		+ 字符串逆序采用 `reverse(res.begin(),res.end());` 函数，需要引入 `#include <algorithm>` 头文件，该函数可以用于翻转数组、字符串和向量（`vector`）。
+ 代码：

```cpp
class Solution {
public:
    string removeDuplicates(string s) {
        stack<char> stk;
        for(int i=0;i<s.size();i++)
        {
            if(stk.empty() || s[i] != stk.top())
                stk.push(s[i]);
            else
                stk.pop();
        }
        string res = "";
        while(!stk.empty())
        {
            res += stk.top();
            stk.pop();
        }
        reverse(res.begin(),res.end());//此时需要反转最终的字符串
        return res;
    }
};
```

### 题目四
+ 逆波兰表达式求值：[150. 逆波兰表达式求值](https://leetcode.cn/problems/evaluate-reverse-polish-notation/description/)
+ 注意点：将 `string` 变量转换为 `int` 型、`long` 型、`long long` 型变量可以使用 `std::stoi`、`std::stol` 和 `std::stoll` 函数！
+ 代码：

```cpp
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<long long> stk;
        long long num1,num2;
        for(int i=0;i<tokens.size();i++)
        {
            if(tokens[i]=="+" || tokens[i]=="-" || 
               tokens[i]=="*" || tokens[i]=="/")
            {
                num1 = stk.top();
                stk.pop();
                num2 = stk.top();
                stk.pop();
                switch(tokens[i][0])
                {
                    case '+':
                        stk.push(num2 + num1);
                    break;
                    case '-':
                        stk.push(num2 - num1);
                    break;
                    case '*':
                        stk.push(num2 * num1);
                    break;
                    case '/':
                        stk.push(num2 / num1);
                    break;
                }
            }
            else
                stk.push(stoi(tokens[i]));
        }
        return stk.top();
    }
};
```

### 题目五
+ 滑动窗口最大值：[239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)
+ 思路：
	+ 实现单调队列，并且该队列没有必要维护窗口里的所有元素，只需要维护有可能成为窗口里最大值的元素就可以了，同时保证队列里的元素数值是由大到小的。
	+ 设计单调队列的时候，`pop`，和 `push` 操作要保持如下规则：
		 1. `pop(value)`：如果窗口移除的元素 `value` 等于单调队列的出口元素，那么队列弹出元素，否则不用任何操作；
		 2. `push(value)`：如果 `push` 的元素 `value` 大于入口元素的数值，那么就将队列入口的元素弹出，直到 `push` 元素的数值小于等于队列入口元素的数值为止。
		 3. 保持如上规则，每次窗口移动的时候，只要问 `que.front()` 就可以返回当前窗口的最大值。
+ 单调队列实现代码：

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

+ 代码：

```cpp
class Solution {
private:
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
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        MyQueue que;
        vector<int> result;
        for (int i = 0; i < k; i++) { // 先将前k的元素放进队列
            que.push(nums[i]);
        }
        result.push_back(que.front()); // result 记录前k的元素的最大值
        for (int i = k; i < nums.size(); i++) {
            que.pop(nums[i - k]); // 滑动窗口移除最前面元素
            que.push(nums[i]); // 滑动窗口前加入最后面的元素
            result.push_back(que.front()); // 记录对应的最大值
        }
        return result;
    }
};
```

+ 时间复杂度: $O(n)$，空间复杂度: $O(k)$。

### 题目六
+ 前 K 个高频元素：[347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/description/)
+ 思路：
	+ 要用小顶堆（采用优先队列实现），因为要统计最大前 `k` 个元素，只有小顶堆每次将最小的元素弹出，最后小顶堆里积累的才是前 `k` 个最大元素。
	+ 寻找前 `k` 个最大元素流程如图所示：（图中的频率只有三个，所以正好构成一个大小为 `3` 的小顶堆，如果频率更多一些，则用这个小顶堆进行扫描）

![使用小顶堆寻找前K个高频元素流程](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202509061431129.png)

+ 代码：

```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        class cmp{
            public:
                bool operator() (const pair<int, int>& lhs, const pair<int, int>& rhs){
                    return lhs.second > rhs.second; // 大于号从大到小排序（小顶堆）
                }
        };
        // 要统计元素出现频率
        unordered_map<int, int> mp;
        for(int i=0; i<nums.size(); i++){
            mp[nums[i]]++;
        }
        // 对频率排序
        // 定义一个小顶堆，大小为k
        priority_queue<pair<int, int>, vector<pair<int, int>>, cmp> pri_que;
        // 用固定大小为k的小顶堆，扫描所有频率的数值
        for(unordered_map<int, int>::iterator it=mp.begin(); it!=mp.end(); it++){
            pri_que.push(*it);
            if(pri_que.size()>k)
                pri_que.pop();
        }
        // 找出前K个高频元素，因为小顶堆先弹出的是最小的，所以倒序来输出到数组
        vector<int> result(k, 0);
        for(int i=result.size()-1; i>=0; i--){
            result[i] = pri_que.top().first;
            pri_que.pop();
        }
        return result;
    }
};
```

### 题目七
+ 数组中第 K 个最大元素：[215. 数组中第 K 个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/description/?envType=study-plan-v2&envId=top-100-liked)
+ 思路：这道题和上一题一样采用小顶堆的方法，令小顶堆的大小为 `k`，当小顶堆满且当前遍历的数组元素大于堆顶元素时（`nums[i] > pri_que.top()` ），弹出堆顶元素，再将该数组元素入堆。
	+ 最后堆顶元素即为答案。
+ 代码：

```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        class cmp{
            public:
                bool operator() (const int& lhs, const int& rhs){
                    return lhs > rhs; // 大于号从大到小排序（小顶堆）
                }
        };
        // 构建优先队列
        priority_queue<int, vector<int>, cmp> pri_que;
        for(int i=0; i<nums.size(); i++){
            if(pri_que.size()<k)
                pri_que.push(nums[i]);
            else if(nums[i] > pri_que.top()){
                pri_que.pop();
                pri_que.push(nums[i]);
            }
        }
        // 取出结果
        return pri_que.top();
    }
};
```

## 二叉树
### 二叉树的递归遍历
+ 前序遍历：[144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/description/)

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
    //前序遍历
    void Traversal(TreeNode* root, vector<int>& ans){
        //终止条件
        if(root == nullptr)
            return;
        ans.push_back(root->val); 
        Traversal(root->left, ans); // 遍历左节点
        Traversal(root->right, ans); // 遍历右节点
    }

    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        Traversal(root, result);
        return result;
    }
};
```

+ 中序遍历：[94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/description/)

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
    // 中序遍历
    void Traversal(TreeNode* root, vector<int>& ans){
        //终止条件
        if(root == nullptr)
            return;
        Traversal(root->left, ans);
        ans.push_back(root->val);
        Traversal(root->right, ans);
    }

    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        Traversal(root, result);
        return result;
    }
};
```

+ 后序遍历：[145. 二叉树的后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/description/)

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
    // 后序遍历
    void Traversal(TreeNode* root, vector<int>& ans){
        //终止条件
        if(root == nullptr)
            return;
        Traversal(root->left, ans);// 左节点
        Traversal(root->right, ans);// 右节点
        ans.push_back(root->val);
    }

    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> result;
        Traversal(root, result);
        return result;
    }
};
```

### 二叉树的迭代遍历
 + 前序遍历迭代法：[144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/description/)

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
    //前序遍历迭代法
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        // 根节点为空
        if(root == nullptr)
            return result;
        stack<TreeNode*> st;
        st.push(root);
        while(!st.empty()){
            TreeNode* node = st.top();
            st.pop();
            result.push_back(node->val);
            if(node->right)
                st.push(node->right);
            if(node->left)
                st.push(node->left);
        }
        return result;
    }
};
```

+ 中序遍历迭代法：[94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/description/)
	+ 由于在中序遍历的过程中，要访问的元素和要处理的元素顺序不一致，在使用迭代法写中序遍历，就需要借用指针的遍历来帮助访问节点，栈则用来处理节点上的元素。

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
    // 中序遍历迭代法
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        if(root == nullptr)
            return result;
        stack<TreeNode*> st;
        TreeNode* cur = root; // 借用指针来区分访问和处理节点的不一致
        while(cur != nullptr || !st.empty()){
            if(cur != nullptr){ // 指针来访问节点，访问到最底层
                st.push(cur);
                cur = cur->left; //持续往左节点访问直到最底层
            }
            else{
                cur = st.top();
                st.pop();
                result.push_back(cur->val); // 中
                cur = cur->right; // 右
            }
        }
        return result;
    }
};
```

+ 后序遍历迭代法：[145. 二叉树的后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/description/)
	+ 前序遍历顺序：**中左右**；
	+ 后序遍历顺序：**左右中**；
	+ 那么可以简单修改前序遍历顺序实现**中右左**，然后再**反转答案数组**即可。

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
    // 后序遍历迭代法
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> result;
        // 根节点为空
        if(root == nullptr)
            return result;
        stack<TreeNode*> st;
        st.push(root);
        while(!st.empty()){
            TreeNode* node = st.top();
            st.pop();
            result.push_back(node->val);
            //因为栈后进先出，以中右左的顺序
            if(node->left)
                st.push(node->left);
            if(node->right)
                st.push(node->right);
        }
        //反转结果，变为左右中的顺序
        reverse(result.begin(), result.end());
        return result;
    }
};
```

### 二叉树的统一迭代遍历
+ 空指针标记法：就是要处理的节点放入栈之后，紧接着放入一个空指针作为标记，三种不同的遍历方式只需修改少量代码。
+  中序遍历迭代法：[94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/description/)

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
    // 中序遍历统一迭代法
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        if(root == nullptr)
            return result;
        else
            st.push(root);
        while(!st.empty()){
            TreeNode* node = st.top(); // 取出栈顶节点
            if(node != nullptr){ // node 不是空节点
                st.pop(); // 节点先要弹出，避免后续重复操作
                // 先入右节点，符合栈的顺序
                if(node->right)
                    st.push(node->right);
                // 中间节点，加上 null 表示访问过但是没处理
                st.push(node);
                st.push(nullptr);
                //再入左节点，符合栈的顺序
                if(node->left)
                    st.push(node->left);
            }
            else{ // 遇到空节点表示将下一个节点加到结果向量中
                st.pop();
                node = st.top(); //重新取出栈顶元素
                st.pop();
                result.push_back(node->val);
            }
        }
        return result;
    }
};
```

+ 前序遍历迭代法：[144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/description/)

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
    //前序遍历统一迭代法
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        if(root == nullptr)
            return result;
        else
            st.push(root);
        while(!st.empty()){
            TreeNode* node = st.top(); // 取出栈顶节点
            if(node != nullptr){ // node 不是空节点
                st.pop(); // 节点先要弹出，避免后续重复操作
                // 先入右节点，符合栈的顺序
                if(node->right)
                    st.push(node->right);
                //再入左节点，符合栈的顺序
                if(node->left)
                    st.push(node->left);
                // 中间节点，加上 null 表示访问过但是没处理
                st.push(node);
                st.push(nullptr);
            }
            else{ // 遇到空节点表示将下一个节点加到结果向量中
                st.pop();
                node = st.top(); //重新取出栈顶元素
                st.pop();
                result.push_back(node->val);
            }
        }
        return result;
    }
};
```

+ 后序遍历迭代法：[145. 二叉树的后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/description/)

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
    // 后序遍历统一迭代法
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> result;
        stack<TreeNode*> st;
        if(root == nullptr)
            return result;
        else
            st.push(root);
        while(!st.empty()){
            TreeNode* node = st.top(); // 取出栈顶节点
            if(node != nullptr){ // node 不是空节点
                st.pop(); // 节点先要弹出，避免后续重复操作
                // 中间节点，加上 null 表示访问过但是没处理
                st.push(node);
                st.push(nullptr);
                // 先入右节点，符合栈的顺序
                if(node->right)
                    st.push(node->right);
                //再入左节点，符合栈的顺序
                if(node->left)
                    st.push(node->left);
            }
            else{ // 遇到空节点表示将下一个节点加到结果向量中
                st.pop();
                node = st.top(); //重新取出栈顶元素
                st.pop();
                result.push_back(node->val);
            }
        }
        return result;
    }
};
```

### 二叉树的层序遍历
+ 二叉树的层序遍历需要借用一个辅助数据结构即队列来实现：
	+ **队列先进先出，符合一层一层遍历的逻辑**，而用栈先进后出适合模拟深度优先遍历也就是递归的逻辑。
+ 思路：
	+ 首先要记录这一层中有多少个结点，因此要定义一个确定的 `size`。
	+ 使用 `for` 循环来遍历 `size` 数量个结点，将每个结点弹出，并将其数值传入结果数组中，将其左右孩子入队列，作为下一层的遍历内容。
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
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> result;
        queue<TreeNode*> que;
        if(root == nullptr)
            return result;
        que.push(root);
        while(!que.empty()){
            vector<int> ans; // 一层中的内容
            int size = que.size(); //  size 用于确定这一层中的节点数量
            for(int i=0; i<size; i++){
                TreeNode* node = que.front();
                que.pop();
                ans.push_back(node->val);
                if(node->left)
                    que.push(node->left);
                if(node->right)
                    que.push(node->right);
            }
            result.push_back(ans);
        }
        return result;
    }
};
```

### 翻转二叉树
+ 翻转二叉树：[226. 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)
+ 思路：
	+ 递归法：采用**前序、后序递归比较合适**，中序会造成左孩子翻转两次。
	+ 层序遍历法也可以。
+ 递归法前序遍历代码：

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
        if(root == nullptr)
            return root;
        swap(root->left, root->right);
        invertTree(root->left);
        invertTree(root->right);
        return root;
    }
};
```

+ 递归法后序遍历代码：

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
        if(root == nullptr)
            return root;
        invertTree(root->left);
        invertTree(root->right);
        swap(root->left, root->right);
        return root;
    }
};
```

+ 层序遍历法：

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
        queue<TreeNode*> que;
        if (root != NULL) que.push(root);
        while (!que.empty()) {
            int size = que.size();
            for (int i = 0; i < size; i++) {
                TreeNode* node = que.front();
                que.pop();
                swap(node->left, node->right); // 节点处理
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }
        return root;
    }
};
```

### 对称二叉树
+ 对称二叉树：[101. 对称二叉树](https://leetcode.cn/problems/symmetric-tree/description/)
+ 思路：[代码随想录思路](https://programmercarl.com/0101.%E5%AF%B9%E7%A7%B0%E4%BA%8C%E5%8F%89%E6%A0%91.html#%E6%80%9D%E8%B7%AF)
+ 代码：

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    // 递归判断
    bool compare(TreeNode* left, TreeNode* right){
        if(left == nullptr && right != nullptr)
            return false;
        else if(left != nullptr && right == nullptr)
            return false;
        else if(left == nullptr && right == nullptr)
            return true;
        else if(left->val != right->val)
            return false;
        else // 分为子树内侧和外侧
            return (compare(left->left, right->right) && compare(left->right, right->left));
    }

    bool isSymmetric(TreeNode* root) {
        if(root == nullptr)
            return true;
        return compare(root->left, root->right);
    }
};
```

### 另一棵树的子树
+ 对称二叉树：[572. 另一棵树的子树](https://leetcode.cn/problems/subtree-of-another-tree/description/)
+ 思路：与上一题类似，先写好递归判断两棵子树是否相等 `compare` 函数，再使用层序遍历法逐个遍历主树的节点，并调用 `compare` 函数。
+ 代码：

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    bool compare(TreeNode* root, TreeNode* subRoot){
        if(root == nullptr && subRoot == nullptr)
            return true;
        else if(root != nullptr && subRoot == nullptr)
            return false;
        else if(root == nullptr && subRoot != nullptr)
            return false;
        else if(root->val != subRoot->val)
            return false;
        else{
            return (compare(root->left, subRoot->left) && compare(root->right, subRoot->right));
        }
    }
	//层序遍历寻找子树
    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        if(root == nullptr && subRoot == nullptr)
            return true;
        else if(root == nullptr && subRoot != nullptr)
            return false;
        queue<TreeNode*> que;
        que.push(root);
        while(!que.empty()){
            TreeNode* node = que.front();
            int size = que.size();
            bool ans;
            for(int i=0; i<size; i++){
                node = que.front();
                que.pop();
                ans = compare(node, subRoot);
                if(ans)
                    return true;
                if(node->left)
                    que.push(node->left);
                if(node->right)
                    que.push(node->right);
            }
        }
        return false;
    }
};
```

+ 递归遍历法代码：

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
    bool compare(TreeNode* root, TreeNode* subRoot){
        if(root == nullptr && subRoot == nullptr)
            return true;
        else if(root != nullptr && subRoot == nullptr)
            return false;
        else if(root == nullptr && subRoot != nullptr)
            return false;
        else if(root->val != subRoot->val)
            return false;
        else{
            return (compare(root->left, subRoot->left) && compare(root->right, subRoot->right));
        }
    }

    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        if (subRoot == nullptr) {
            return root == nullptr;
        }
        if (root == nullptr) {
            return false;
        }
        if (compare(root, subRoot)) {
            return true;
        }
        return isSubtree(root->left, subRoot) || isSubtree(root->right, subRoot);
    }
};
```

### 二叉树的最大深度
+ 二叉树的最大深度：[104.二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)
+ 递归思路：求二叉树的最大深度就是求二叉树的最大高度，可以使用后序遍历的方式递归求节点 `root` 左右子树的最大高度，然后加 `1` 返回节点 `root` 的最大高度。
+ 递归代码：

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
    int getMaxDepth(TreeNode* root){
        if(root == nullptr)
            return 0;
        int leftDepth = getMaxDepth(root->left);
        int rightDepth = getMaxDepth(root->right);
        // 返回节点root左右子树的最大高度，然后加1返回节点root的最大高度
        return 1 + max(leftDepth, rightDepth); 
    }
    
    int maxDepth(TreeNode* root) {
        return getMaxDepth(root);
    }
};
```

+ 层序遍历代码：

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == NULL) return 0;
        int depth = 0;
        queue<TreeNode*> que;
        que.push(root);
        while(!que.empty()) {
            int size = que.size();
            depth++; // 记录深度
            for (int i = 0; i < size; i++) {
                TreeNode* node = que.front();
                que.pop();
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }
        return depth;
    }
};
```

### 二叉树的最小深度
+ 二叉树的最小深度：[111.二叉树的最小深度](https://leetcode.cn/problems/minimum-depth-of-binary-tree/description/)
+ 递归思路：需要关注节点 `root` 左右孩子节点中有一个为空的情况，做特殊处理。而当节点 `root` 有左右子树的时候取左右子树的最小高度，然后加 `1` 返回节点 `root` 的最小高度。
+ 递归代码：

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
    int getMinDepth(TreeNode* root){
        if(root == nullptr)
            return 0;
        int leftDepth = getMinDepth(root->left);
        int rightDepth = getMinDepth(root->right);
        if(root->left == nullptr && root->right != nullptr)
            return 1 + rightDepth;
        else if(root->left != nullptr && root->right == nullptr)
            return 1 + leftDepth;
        else
            return 1 + min(leftDepth, rightDepth);
    }

    int minDepth(TreeNode* root) {
        return getMinDepth(root);
    }
};
```

+ 层序遍历法代码：

```cpp
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (root == NULL) return 0;
        int depth = 0;
        queue<TreeNode*> que;
        que.push(root);
        while(!que.empty()) {
            int size = que.size();
            depth++; // 记录最小深度
            for (int i = 0; i < size; i++) {
                TreeNode* node = que.front();
                que.pop();
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
                if (!node->left && !node->right) { // 当左右孩子都为空的时候，说明是最低点的一层了，退出
                    return depth;
                }
            }
        }
        return depth;
    }
};
```

### 平衡二叉树
+ 平衡二叉树：[110.平衡二叉树](https://leetcode.cn/problems/balanced-binary-tree/description/)
+ 递归思路：思路与求最大深度类似，递归找节点 `root` 左右子树的最大高度，然后在递归中进行处理，如果在该节点 `root` 已经不满足平衡二叉树的时候就返回 `-1`，否则求节点 `root` 左右子树的最大高度，然后加 `1` 返回节点 `root` 的最大高度。再加上单独处理 `-1` 的情况即可。
+ 递归代码：

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
    int getHeight(TreeNode* root){
        if(root == nullptr)
            return 0;
        int leftHeight = getHeight(root->left);
        if(leftHeight == -1)
            return -1;
        int rightHeight = getHeight(root->right);
        if(rightHeight == -1)
            return -1;
        int result;
        if(abs(leftHeight - rightHeight) > 1)
            result = -1;
        else
            result = 1 + max(leftHeight, rightHeight);
        return result;
    }

    bool isBalanced(TreeNode* root) {
        int ans = getHeight(root);
        if(ans == -1)
            return false;
        else
            return true;
    }
};
```

### 二叉树的所有路径
+ 二叉树的所有路径：[257.二叉树的所有路径](https://leetcode.cn/problems/binary-tree-paths/description/)
+ 思路：这道题目要求从根节点到叶子的路径，所以需要前序遍历，这样才方便让父节点指向孩子节点，找到对应的路径。同时要使用到回溯把路径记录下来，需要回溯来回退一个路径再进入另一个路径。
+ 代码：

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    void travelsal(TreeNode* node, vector<int>& path, vector<string>& result){
        // 中，为什么写在这里，因为最后一个节点也要加入到path中
        path.push_back(node->val); // 传入节点数值
        // 递归终止条件，到达叶子节点
        if(node->left == nullptr && node->right == nullptr){
            string sPath;
            int size = path.size();
            for(int i=0; i<size-1; i++){
                sPath += to_string(path[i]);
                sPath += "->";
            }
            sPath += to_string(path[size-1]);
            result.push_back(sPath);
            return;
        }
        //左
        if(node->left){
            travelsal(node->left, path, result);
            // 回溯，path是全局变量，要把递归的后一个节点弹出
            path.pop_back(); 
        }
        // 右
        if(node->right){
            travelsal(node->right, path, result);
            // 回溯，path是全局变量，要把递归的后一个节点弹出
            path.pop_back(); 
        }
    }
    
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> result;
        if(root == nullptr)
            return result;
        vector<int> path;
        travelsal(root, path, result);
        return result;
    }
};
```

+ 代码也可以进行精简：

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

### 找树左下角的值
+ 找树左下角的值：[513.找树左下角的值](https://leetcode.cn/problems/find-bottom-left-tree-value/description/)
+ 思路：这道题目使用层序遍历法十分直观，而使用递归法需要进行回溯，找到二叉树的最大深度的第一个节点并做记录。
+ 层序遍历法：

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
    int findBottomLeftValue(TreeNode* root) {
        int ans = 0;
        queue<TreeNode*> que;
        que.push(root);
        while(!que.empty()){
            int size = que.size();
            TreeNode* node = que.front();
            for(int i=0; i<size; i++){
                node = que.front();
                que.pop();
                if(i == 0)
                    ans = node->val;
                if(node->left)
                    que.push(node->left);
                if(node->right)
                    que.push(node->right);
            }
        }
        return ans;
    }
};
```

+ 递归回溯法：

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
    int maxDepth = INT_MIN;
    void travelsal(TreeNode* node, int depth, int& ans){
        // 终止条件，到达叶子节点
        if(node->left == nullptr && node->right == nullptr){
            if(depth > maxDepth){
                maxDepth = depth;
                ans = node->val;
                return ;
            }
        }
        if(node->left){
            depth++;
            travelsal(node->left, depth, ans);
            depth--; // 回溯的过程
        }
        if(node->right){
            depth++;
            travelsal(node->right, depth, ans);
            depth--; // 回溯的过程
        }
    }
    
    int findBottomLeftValue(TreeNode* root) {
        int result = 0;
        int depth = 1;
        travelsal(root, depth, result);
        return result;
    }
};
```

### 从中序与后序遍历序列构造二叉树
+ 从中序与后序遍历序列构造二叉树：[106.从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/)
+ 思路：
	+ 第一步：如果数组大小为零的话，说明是空节点了；
	+ 第二步：如果不为空，那么取后序数组最后一个元素作为中间节点 `root` ；
	+ 第三步：判断数组大小是否为 `1`，是的话到达叶子节点，可以直接返回；
	+ 第四步：找到**后序数组**最后一个元素在**中序数组**的位置索引 `index` ，作为切割点；
	+ 第五步：分割中序遍历数组，均采用左闭右开，注意迭代器 `inorder.end()` 表示数组中最后一个元素的后一个；
	+ 第六步：分割后序遍历数组，同样需要想清楚区间；
	+ 第七步，递归左右子树并返回中间节点 `root` 。
+ 代码：

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* travelsal(vector<int>& inorder, vector<int>& postorder){
        // 如果后序数组为空
        if(postorder.size() == 0)
            return nullptr;
        // 后序数组的最后一个节点就是中间节点
        int rootValue = postorder[postorder.size()-1];
        TreeNode* root = new TreeNode(rootValue); // 中间节点数值
        // 到达叶子节点
        if(postorder.size() == 1)
            return root;
        // 找分割数组的索引
        int index = 0; // 找分割点
        for(int i=0; i<inorder.size(); i++){
            if(inorder[i] == rootValue){
                index = i;
                break;
            }
        }
        // 分割中序数组
        // [begin, begin+index)
        vector<int> leftInorder(inorder.begin(), inorder.begin()+index); 
        // [begin+index+1, end) end是容器尾部元素的下一位
        vector<int> rightInorder(inorder.begin()+index+1, inorder.end()); 
        // 分割后序数组
        // [begin, begin+index) 
        vector<int> leftPostorder(postorder.begin(), postorder.begin()+index); 
        // [begin+index, end-1) end是容器尾部元素的下一位
        vector<int> rightPostorder(postorder.begin()+index, postorder.end()-1); 
        // 递归左子树
        root->left = travelsal(leftInorder, leftPostorder);
        // 递归右子树
        root->right = travelsal(rightInorder, rightPostorder);
        return root;
    }
    
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        return travelsal(inorder, postorder);
    }
};
```

+ 从中序与前序遍历序列构造二叉树：[105.从中序与前序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/)
+ 思路：与上面的题目思路类似！
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
    TreeNode* travelsal(vector<int>& preorder, vector<int>& inorder){
        // 空节点
        if(preorder.size() == 0)
            return nullptr;
        // 记录先序遍历的第一个节点作为中间节点
        int rootValue = preorder[0];
        TreeNode* root = new TreeNode(rootValue);
        // 到达叶子节点
        if(preorder.size() == 1)
            return root;
        // 从中序遍历中寻找中间节点左右子树的索引
        int index = 0;
        for(int i=0; i<inorder.size(); i++){
            if(inorder[i] == rootValue){
                index = i;
                break;
            }
        }
        // 分割中序遍历数组
        // [begin, begin+index)
        vector<int> leftInorder(inorder.begin(), inorder.begin()+index);
        // [begin+index+1, end)
        vector<int> rightInorder(inorder.begin()+index+1, inorder.end());
        // 分割前序遍历数组
        // [begin+1, begin+1+index)
        vector<int> leftPreorder(preorder.begin()+1, preorder.begin()+1+index);
        // [begin+1+index, end)
        vector<int> rightPreorder(preorder.begin()+1+index, preorder.end());
        // 递归左右子树
        root->left = travelsal(leftPreorder, leftInorder);
        root->right = travelsal(rightPreorder, rightInorder);
        return root;
    }
    
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return travelsal(preorder, inorder);
    }
};
```

### 最大二叉树
+ 最大二叉树：[654.最大二叉树](https://leetcode.cn/problems/maximum-binary-tree/description/)
+ 思路：这道题目与前面一道题目类似，依然采用前序遍历的方式，首先构建根节点然后再递归构建左右子树，需要注意的是需要遍历寻找数组中最大值，然后依据改数值将数组划分为左右两部分，将区间索引传入左右子树的递归中。
+ 代码：

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    // 左闭右闭 [left, right]
    TreeNode* travelsal(const vector<int>& nums, int left, int right){
        // 终止条件
        if(right == left){
            TreeNode* root = new TreeNode(nums[left]);
            return root;
        }
        // 中：找到最大值和索引分为左右子树
        int maxValue = 0;
        int index = 0;
        for(int i=left; i<=right; i++){
            if(nums[i]>maxValue){
                maxValue = nums[i];
                index = i;
            }
        }
        // 以最大值构造根节点
        TreeNode* root = new TreeNode(maxValue);
        // 递归左子树
        if(index > left){
            root->left = travelsal(nums, left, index-1);
        }
        // 递归右子树
        if(index<right){
            root->right = travelsal(nums, index+1, right);
        }
        return root;
    }
    
    TreeNode* constructMaximumBinaryTree(vector<int>& nums) {
        return travelsal(nums, 0, nums.size()-1);
    }
};
```

### 合并二叉树
+ 合并二叉树：[617.合并二叉树](https://leetcode.cn/problems/merge-two-binary-trees/description/)
+ 思路：同样采用前序遍历，在 `root1` 的基础上合并二叉树，终止条件为有一个节点为空节点的时候，返回另一个课子树。递归逻辑在于将 `root2` 的数值加到 `root1` 中合并为根节点，然后从根节点出发递归遍历左右子树。
+ 代码：

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* travelsal(TreeNode* root1, TreeNode* root2){
        // 终止条件
        if(root1 == nullptr)
            return root2;
        if(root2 == nullptr)
            return root1;
        // 中：在root1的基础上加上root2的值
        root1->val += root2->val;
        // 遍历左子树
        root1->left = travelsal(root1->left, root2->left);
        // 遍历右子树
        root1->right = travelsal(root1->right, root2->right);
        return root1;
    }
    
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        return travelsal(root1, root2);
    }
};
```

### 二叉搜索树的搜索
+ 二叉搜索树的搜索：[700.二叉搜索树的搜索](https://leetcode.cn/problems/search-in-a-binary-search-tree/description/)
+ 思路：终止条件为遇到空节点或者找到要搜索的数值，因为该二叉树是二叉搜索树，所以左子树的数值一定小于根节点数值，根节点数值一定小于右子树数值，因此可以选择性地向左右子树进行递归来寻找。
+ 代码：

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* travelsal(TreeNode* root, int val){
        // 终止条件
        if(root == nullptr)
            return nullptr;
        if(root->val == val)
            return root;
        // 递归遍历左右子树
        TreeNode* ans = nullptr;
        if(root->val > val)
            ans = travelsal(root->left, val);
        if(root->val < val)
            ans = travelsal(root->right, val);
        return ans;
    }
    
    TreeNode* searchBST(TreeNode* root, int val) {
        return travelsal(root, val);
    }
};
```

### 验证二叉搜索树
+ 验证二叉搜索树：[98.验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/description/)
+ 思路：要想验证这棵二叉树是否为二叉搜索树，只需要中序遍历该树得到遍历后的元素数组，再验证这个数组是否为升序的即可。
+ 代码：

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    void inorder(TreeNode* root, vector<int>& arr){
        // 终止条件
        if(root == nullptr)
            return;
        inorder(root->left, arr);
        arr.push_back(root->val);
        inorder(root->right, arr);
    }
    
    bool isValidBST(TreeNode* root) {
        vector<int> arr;
        // 中序遍历将二叉搜索树转换为有序数组
        inorder(root, arr);
        for(int i=0; i<arr.size()-1; i++){
             // 注意要小于等于，搜索树里不能有相同元素
            if(arr[i+1] <= arr[i])
                return false;
        }
        return true;
    }
};
```

### 二叉搜索树的最小绝对差
+ 二叉搜索树的最小绝对差：[530.二叉搜索树的最小绝对差](https://leetcode.cn/problems/minimum-absolute-difference-in-bst/description/)
+ 两种思路：
	+ 一是类似上一题验证二叉搜索树的方法利用中序遍历得到升序数组，再遍历数组找出最小差值；
	+ 二是利用双指针法，只需要采用中序遍历一次二叉搜索树即可，但需要注意一些细节。
+ 思路一代码：

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
    vector<int> result;
    // 中序遍历
    void inorder(TreeNode* root){
        // 终止条件
        if(root == nullptr)
            return;
        // 左
        inorder(root->left);
        // 中
        result.push_back(root->val);
        // 右
        inorder(root->right);
    }

    int getMinimumDifference(TreeNode* root) {
        // 先进行中序遍历
        inorder(root);
        int minValue = INT_MAX;
        for(int i=0; i<result.size()-1; i++){
            minValue = min(minValue, result[i+1] - result[i]);
        }
        return minValue;
    }
};
```

+ 思路二代码：

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
    int minValue = INT_MAX;
    // 指向前一个指针
    TreeNode* pre = nullptr;
    void travelsal(TreeNode* cur){
        // 终止条件
        if(cur == nullptr)
            return;
        // 左
        travelsal(cur->left);
        // 中
        if(pre != nullptr)
            minValue = min(minValue, abs(cur->val - pre->val));
        // 将pre指针指向cur的前一个
        pre = cur;
        // 右
        travelsal(cur->right);
    }

    int getMinimumDifference(TreeNode* root) {
        travelsal(root);
        return minValue;
    }
};
```

### 二叉搜索树的众数
+ 二叉搜索树的众数：[501.二叉搜索树的众数](https://leetcode.cn/problems/find-mode-in-binary-search-tree/)
+ 思路：这题跟上面一题可以采用双指针法，使用 `cur` 指针指向当前节点，`pre` 指针指向 `cur` 的前一个节点，需要注意一下 `count` 和 `maxCount` 的数值变化和 `result` 数组的处理情况。
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
    int count = 0;
    int maxCount = 0;
    vector<int> result;
    TreeNode* pre = nullptr;
    void travelsal(TreeNode* cur){
        // 终止条件
        if(cur == nullptr)
            return;
        // 左
        travelsal(cur->left);
        // 中
        if(pre == nullptr) // 第一个节点
            count = 1;
        else if(pre->val == cur->val) // 两个节点数值相同
            count++;
        else // 发现新节点数值
            count = 1;
        // pre指向cur的前一个节点
        pre = cur;
        if(count == maxCount)
            result.push_back(cur->val);
        if(count > maxCount){
            result.clear();
            maxCount = count;
            result.push_back(cur->val);
        }
        // 右
        travelsal(cur->right);
    }

    vector<int> findMode(TreeNode* root) {
        travelsal(root);
        return result;
    }
};
```

### 二叉树的最近公共祖先
+ 二叉树的最近公共祖先：[236.二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/description/)
+ 思路：采用后续遍历，终止条件为遇到空节点就返回 `nullptr`，或者找到了 `p` 或者 `q` 的节点，就返回当前节点 `root` 。然后递归处理左右节点，处理中间节点时有以下判断：
	+ 左节点和右节点是否都不为空，该中间节点就是最近公共祖先，返回 `root`。
	+ 左节点或者右节点其中一个为空另外一个不为空，返回不为空的节点。
	+ 左右节点都为空，返回空节点。
+ 代码：

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
    // 后续遍历
    TreeNode* travelsal(TreeNode* root, TreeNode* p, TreeNode* q){
        // 终止条件
        if(root == nullptr)
            return nullptr;
        if(root == q || root == p)
            return root;
        // 左
        TreeNode* left = travelsal(root->left, p, q);
        // 右
        TreeNode* right = travelsal(root->right, p, q);
        // 中
        if(left != nullptr && right != nullptr)
            return root;
        else if(left == nullptr && right != nullptr)
            return right;
        else if(left != nullptr && right == nullptr)
            return left;
        else
            return nullptr;
    }
    
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        TreeNode* ans = travelsal(root, p, q);
        return ans;
    }
};
```

### 二叉搜索树的最近公共祖先
+ 二叉搜索树的最近公共祖先：[235.二叉搜索树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/)
+ 思路：利用二叉搜索树的特性：
	+ 如果 `p` 和 `q` 的值都小于 `root` 的值，就往左子树搜索；
	+ 如果 `p` 和 `q` 的值都大于 `root` 的值，就往右子树搜索；
	+ 如果 `p` 和分别在 `root` 的两端或者 `root` 就是 `p` 和 `q` 中的一个节点，就返回 `root`，`root` 一定是 `p` 和 `q` 的最近公共祖先。
+ 代码：

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
    TreeNode* travelsal(TreeNode* root, TreeNode* p, TreeNode* q){
        // 终止条件
        if(root == nullptr)
            return nullptr;
        TreeNode* left = nullptr;
        TreeNode* right = nullptr;
        // 左
        if(p->val < root->val && q->val < root->val){
            left = travelsal(root->left, p, q);
        }// 右
        else if(p->val > root->val && q->val > root->val){
            right = travelsal(root->right, p, q);
        }
        // 中
        if(left != nullptr)
            return left;
        else if(right != nullptr)
            return right;
        else
            return root;
    }

    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        TreeNode* ans = travelsal(root, p, q);
        return ans;
    }
};
```

### 二叉搜索树中的插入操作
+ 二叉搜索树中的插入操作：[701.二叉搜索树中的插入操作](https://leetcode.cn/problems/insert-into-a-binary-search-tree/description/)
+ 思路：因为本题是二叉搜索树，并且不限制插入的方式，因此按照二叉搜索树的规则遍历，找到空节点插入即可。这道题采用递归方式和迭代方式都比较方便，同样是按照二叉搜索树的规则往下搜寻得到空间点，再将空节点插入即可。
+ 递归法代码：

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
    // 递归法插入
    TreeNode* insert(TreeNode* root, int val){
        // 终止条件
        if(root == nullptr){
            root = new TreeNode(val);
            return root;
        }
        // 向左递归
        if(val < root->val){
            root->left = insert(root->left, val);
        }
        // 向右递归
        if(val > root->val){
            root->right = insert(root->right, val);
        }
        return root;
    }

    TreeNode* insertIntoBST(TreeNode* root, int val) {
        TreeNode* ans = insert(root, val);
        return ans;
    }
};```

+ 迭代法代码：

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
	// 迭代法
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        TreeNode* cur = root;
        if(root == nullptr)
            root = new TreeNode(val);
        while(cur != nullptr){
            if(val < cur->val){
                if(cur->left != nullptr)
                    cur = cur->left;
                else{
                    TreeNode* node = new TreeNode(val);
                    cur->left = node;
                    cur = nullptr;
                }
            }
            else if(val > cur->val){
                if(cur->right != nullptr)
                    cur = cur->right;
                else{
                    TreeNode* node = new TreeNode(val);
                    cur->right = node;
                    cur = nullptr;
                }
            }
        }
        return root;
    }
};
```

### 删除二叉搜索树中的节点
+ 删除二叉搜索树中的节点：[450.删除二叉搜索树中的节点](https://leetcode.cn/problems/delete-node-in-a-bst/description/)
+ 思路：
	+ 终止条件有以下五种情况：
		+ 第一种情况：没找到删除的节点，遍历到空节点直接返回了；
		+ 找到删除的节点：
			+ 第二种情况：左右孩子都为空（叶子节点），直接删除节点，返回 `NULL` 为根节点；
			+ 第三种情况：删除节点的左孩子为空，右孩子不为空，删除节点，右孩子补位，返回右孩子为根节点；
			+ 第四种情况：删除节点的右孩子为空，左孩子不为空，删除节点，左孩子补位，返回左孩子为根节点；
			+ 第五种情况：左右孩子节点都不为空，则将删除节点的左子树头结点（左孩子）放到删除节点的右子树的最左面节点的左孩子上，返回删除节点右孩子为新的根节点。
	+ 根据二叉搜索树的规则递归查找 `key` 节点。
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
    TreeNode* travelsal(TreeNode* root, int key){
        // 终止条件
        // 1.没有找到要删除的节点
        if(root == nullptr){
            return nullptr;
        }
        // 2.删除的是叶子节点
        else if(key == root->val && root->left == nullptr && root->right == nullptr){
            delete root;
            return nullptr;
        }
        // 3.删除的节点只有左子树
        else if(key == root->val && root->left != nullptr && root->right == nullptr){
            TreeNode* temp_node = root->left;
            delete root;
            return temp_node;
        }
        // 4.删除的节点只有右子树
        else if(key == root->val && root->right != nullptr && root->left == nullptr){
            TreeNode* temp_node = root->right;
            delete root;
            return temp_node;
        }
        // 5.删除的节点左右子树都不为空,把左子树嫁接到右子树处
        else if(key == root->val && root->right != nullptr && root->left != nullptr){
            TreeNode* temp_node = root->right;
            while(temp_node->left != nullptr){
                temp_node = temp_node->left;
            }
            temp_node->left = root->left;
            TreeNode* new_root = root->right;
            delete root;
            return new_root;
        }
        // 递归遍历
        if(key < root->val)
            root->left = travelsal(root->left, key);
        if(key > root->val)
            root->right = travelsal(root->right, key);
        return root;
    }

    TreeNode* deleteNode(TreeNode* root, int key) {
        TreeNode* ans = travelsal(root, key);
        return ans;
    }
};
```

### 修剪二叉搜索树
+ 修剪二叉搜索树：[669.修剪二叉搜索树](https://leetcode.cn/problems/trim-a-binary-search-tree/)
+ 思路：
	+ 在终止条件中处理 `root` 节点：
		+ 遍历到 `root` 节点为空的情况则返回空；
		+ 如果 `root` 节点的值小于下限值则继续往右子树遍历查找是否有不在区间的值；
		+ 如果 `root` 节点的值大于下限值则继续往左子树遍历查找是否有不在区间的值。
	+ 之后 `root` 节点在区间内，再递归处理左右孩子不在区间内的节点。
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
    // 返回处理好子树的根节点
    TreeNode* travelsal(TreeNode* root, int low, int high){
        // 终止条件(处理root节点，root节点不在区间内)
        if(root == nullptr)
            return root;
        if(root->val < low){
            return travelsal(root->right, low, high);
        }
        if(root->val > high){
            return travelsal(root->left, low, high);
        }
        // 之后root节点在区间内，处理左右孩子不在区间内的节点
        // 左
        root->left = travelsal(root->left, low, high);
        // 右
        root->right = travelsal(root->right, low, high);
        return root;
    }

    TreeNode* trimBST(TreeNode* root, int low, int high) {
        TreeNode* ans = travelsal(root, low, high);
        return ans;
    }
};
```

### 将有序数组转换为二叉搜索树
+ 将有序数组转换为二叉搜索树：[108.将有序数组转换为二叉搜索树](https://leetcode.cn/problems/convert-sorted-array-to-binary-search-tree/description/)
+ 思路：题目中给出的是按照升序排列的整数数组，只需要每次递归选取数组的中间数值作为根节点 `root`，递归数组左边作为左子树，数组右边作为右子树进行构造即可。
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
    // 左闭右闭区间
    TreeNode* travelsal(vector<int>& nums, int left, int right){
        // 终止条件
        if(left > right)
            return nullptr;
        // 递归构造子树
        int mid = left + (right - left)/2;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = travelsal(nums, left, mid-1);
        root->right = travelsal(nums, mid+1, right);
        return root;
    }

    TreeNode* sortedArrayToBST(vector<int>& nums) {
        TreeNode* ans = travelsal(nums, 0, nums.size()-1);
        return ans;
    }
};
```

### 把二叉搜索树转换为累加树
+ 把二叉搜索树转换为累加树：[538.把二叉搜索树转换为累加树](https://leetcode.cn/problems/convert-bst-to-greater-tree/description/)
+ 思路：这题采用双指针的方法，因为要将比该节点大的所有数值加上，因此采用**右-中-左**的遍历方式，中的逻辑处理相加操作。
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
    int pre = 0;
    void travelsal(TreeNode* cur){
	    // 终止条件
        if(cur == nullptr)
            return;
        // 右
        travelsal(cur->right);
        // 中
        cur->val += pre;
        pre = cur->val;
        // 左
        travelsal(cur->left);
    }
    
    TreeNode* convertBST(TreeNode* root) {
        travelsal(root);
        return root;
    }
};
```

## 排序问题
### 选择排序
#### 普通选择排序
+ 选择排序理论：

![选择排序理论](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250510160136.png)

+ 代码：

```cpp
// 选择排序
void choose_sort(vector<int>& nums)
{
    if(nums.size()==0 || nums.size()==1)
        return;
    int minIndex = 0;
    for(int i=0; i<nums.size()-1; i++)
    {
        minIndex = i;
        for(int j=i+1; j<nums.size(); j++)
        {
            if(nums[j]<nums[minIndex])
                minIndex = j;
        }
        swap(nums[i],nums[minIndex]);
    }
}
```

+ 分析：

![选择排序的分析](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250510210721.png)

### 冒泡排序
#### 普通冒泡排序
+ 冒泡排序理论：

![冒泡排序理论](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250510161815.png)

+ 代码：

```cpp
// 冒泡排序
void bubble_sort(vector<int>& nums)
{
    if(nums.size()==0 || nums.size()==1)
        return;
    int last = nums.size()-1;
    int flag = 0; // 注意点：当不再发生交换的时候就已经有序了
    for(int i=0; i<nums.size(); i++)
    {
        flag = 0;
        for(int j=0; j<last; j++)
        {
            if(nums[j]>nums[j+1])
            {
                swap(nums[j],nums[j+1]);
                flag++;
            }
        }
        if(flag==0)
            break;
    }
}
```

+ 分析：

![冒泡排序的分析](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250510210853.png)

### 插入排序
#### 普通插入排序
+ 插入排序理论：

![插入排序理论](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250510164741.png)

+ 代码：

```cpp
// 插入排序
void insert_sort(vector<int>& nums)
{
    if(nums.size()==0 || nums.size()==1)
        return;
    int value;
    for(int i=1; i<nums.size(); i++) // 要插入元素的索引
    {
        int value = nums[i];
        int j = i - 1;
        while(value < nums[j] && j>=0)
        {
            nums[j+1] = nums[j];
            j--;
        }
        nums[j+1] = value;
    }
}
```

+ 性质和分析：
	+ 在原数组有序的最好情况下，算法时间复杂度是 $O(n)$！
	+ 空间复杂度为 $O(1)$！
	+ 插入排序算法具有**稳定性**（假定在待排序的记录序列中，存在多个具有相同的关键字的记录，若经过排序后，这些记录的相对次序保持不变）

![插入排序分析](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250510165134.png)

+ 使用场景：
	+ 数组长度比较小；
	+ 当数组基本有序（离排好序的最终位置很近），插入排序可以在 $O(n)$ 时间内完成排序！

### 希尔排序
#### 普通希尔排序
+ 希尔排序理论：

![希尔排序理论](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/202505101858190.png)

+ 代码：

```cpp
// 希尔排序
void shell_sort(vector<int> &nums)
{
    int gap = nums.size() / 2;
    while (gap)
    {
        for (int i = gap; i < nums.size(); i++)
        {
            int value = nums[i];
            int j = i - gap;
            // 步骤与从插入排序类似
            while (j >= 0 && nums[j] > value)
            {
                nums[j + gap] = nums[j];
                j -= gap;
            }
            nums[j + gap] = value;
        }
        // 缩小gap
        gap /= 2;
    }
}
```

+ 分析：
	+ 时间复杂度和 `gap` 序列相关，一般而言平均情况小于 $O(n^2)$；
	+ 空间复杂度为 $O(1)$；
	+ 插入排序算法不具有**稳定性**，因为发生了长距离交换，通过牺牲不稳定性来换取时间。

### 归并排序
#### 普通归并排序
+ 递归方法：[912.排序数组](https://leetcode.cn/problems/sort-an-array/description/)
+ 思路：采用二分法和递归的方式实现 $O(NlogN)$ 的归并排序；
+ 代码：

```cpp
class Solution {
public:
    void merge(vector<int>& nums,int L,int M,int R)
    {
        vector<int> temp(R-L+1);
        int i = L,j = M+1,k = 0;
        while(i<=M && j<=R)
        {
            temp[k++] = nums[i]<=nums[j]? nums[i++] : nums[j++];
        }
        //要么i越界了,把j剩下的拷贝进去
        while(j<=R)
            temp[k++] = nums[j++];
        //要么j越界了,把i剩下的拷贝进去
        while(i<=M)
            temp[k++] = nums[i++];
        for(int t=0;t<temp.size();t++)
            nums[L+t] = temp[t];//需要注意nums的区间
    }
    
    void process(vector<int>& nums,int L,int R)
    {
        int mid = L + (R - L)/2;
        //递归边界条件
        if(L == R)
            return;
        process(nums,L,mid);
        process(nums,mid+1,R);
        merge(nums,L,mid,R);
    }

    vector<int> sortArray(vector<int>& nums) {
        process(nums,0,nums.size()-1);
        return nums;
    }
};
```

+ 分析：
	+ 归并排序算法是稳定的，但是空间复杂度比较高；

![归并排序的分析](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250510211156.png)

#### 小和问题
+ 问题描述：
+ 在一个数组中，每一个数的左边比当前数小的数累加起来，叫做这个数组的小和。求一个数组的小和。  
	+ 例子：对于数组 `[1,3,4,2,5]` ，
		+ `1` 左边比 `1` 小的数，没有；
		+ `3` 左边比 `3` 小的数，`1`；
		+ `4` 左边比 `4` 小的数，`1,3`；
		+ `2` 左边比 `2` 小的数，`1`；
		+ `5` 左边比 `5` 小的数，`1,2,3,4`；
		+ 所以小和为 `1+1+3+1+1+2+3+4 = 16` 。
+ 思路：
	+ 此处使用归并排序，在 `merge` 时，由于左右两部分都已经有序，可以确定一侧的数都大于正在比较的数，例如，
	+ 归并 `2 4 5 | 1 3 7` 两个部分时，`2` 比 `3` 小，此时可以确定后面的数都大于 `2`，此时便可以一次性计算小和 `2 * 2` (两个数大于 `2`)，而不用一个个遍历。
+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <vector>
using namespace std;

class Solution {
    public:
        
        int merge(vector<int>& nums,int L,int M,int R)
        {
            vector<int> temp(R-L+1);
            int i = L,j = M+1,k = 0;
            int ans = 0;
            while(i<=M && j<=R)
            {
                //左组小于右组数，把小和累加进去
                ans += nums[i]<nums[j] ? (R-j+1)*nums[i] : 0;
                temp[k++] = nums[i]<nums[j] ? nums[i++] : nums[j++];
            }
            //要么i越界了,把j剩下的拷贝进去
            while(j<=R)
                temp[k++] = nums[j++];
            //要么j越界了,把i剩下的拷贝进去
            while(i<=M)
                temp[k++] = nums[i++];
            for(int t=0;t<temp.size();t++)
                nums[L+t] = temp[t];//需要注意nums的区间
            return ans;
        }
        
        int process(vector<int>& nums,int L,int R)
        {
            int mid = L + (R - L)/2;
            int ans = 0;
            //递归边界条件
            if(L == R)
                return 0;
            ans = process(nums,L,mid) + process(nums,mid+1,R) + merge(nums,L,mid,R);
            return ans;
        }
    
        int smallSum(vector<int>& nums) {
            int ans;
            ans = process(nums,0,nums.size()-1);
            return ans;
        }
    };

int main()
{
    Solution mysolution;
    vector<int> vec = {2,4,5,1,7,3};
    int ans;
    ans = mysolution.smallSum(vec);
    printf("%d\n",ans);

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```
  
#### 逆序对问题
+ 交易逆序对总数：[LCR 170 交易逆序对总数](https://leetcode.cn/problems/sort-an-array/description/)
+ 思路：参考归并排序的算法，区别在于在 `merge` 的时候从右到左进行比较，
	+ 左组数比右组数大时，通过 `ans+=j-(M+1)+1;` 计算逆序对的个数，并将左组数拷贝到 `temp` 数组并左移一位；
	+ 右组数比左组数大时，无需计算逆序对个数，只需要将右组数拷贝到 `temp` 数组并左移一位即可。
	+ 注意递归的边界条件！
+ 代码：

```cpp
#include <cstdio>
#include <cstdlib>
#include <iostream>
#include <vector>
using namespace std;

class Solution {
    public:
        
        int merge(vector<int>& record,int L,int M,int R)
        {
            int ans = 0;
            int i = M;
            int j = R;
            vector<int> temp(R-L+1);
            int k=R-L;
            while(i>=L && j >= M+1)
            {
                if(record[i]>record[j])
                {
                    ans+=j-(M+1)+1;
                    temp[k--] = record[i--];
                }
                else
                {
                    temp[k--] = record[j--];
                }
            }
            while(i>=L)
            {
                temp[k--] = record[i--];
            }
            while(j >= M+1)
            {
                temp[k--] = record[j--];
            }
            for(int t=0;t<temp.size();t++)
                record[L+t] = temp[t];
            return ans;
        }
    
        int process(vector<int>& record,int L,int R)
        {
            
            int mid = L + (R-L)/2;
            int ans = 0;
            //递归边界条件
            if(R==L)
                return 0;
            ans = process(record,L,mid) 
                    + process(record,mid+1,R) 
                    + merge(record,L,mid,R);
            return ans;
        }
    
        int reversePairs(vector<int>& record) {
            int ans = 0;
            if(record.size()==0)
                return 0;
            else
            {
                ans = process(record,0,record.size()-1);
                return ans;
            }
        }
    };

int main()
{
    Solution mysolution;
    vector<int> vec = {9,7,5,4,6};
    int ans;
    ans = mysolution.reversePairs(vec);
    printf("%d\n",ans);

    system("pause"); // 防止运行后自动退出，需头文件stdlib.h
    return 0;
}
```

#### 双倍逆序对问题
+ 题目描述：
	+ 给定一个整数数组 `nums`，统计其中满足以下条件的逆序对数量：
	- 条件：存在两个下标 `i` 和 `j`（ `i < j` ），使得 `nums[i] > 2 * nums[j]`。要求设计一个时间复杂度为 $O(NlogN)$ 的算法解决此问题。
	- 示例：
		- 输入：`nums = [8, 3, 5, 1, 2]`  
		- 输出：`6`  
		- 解释：  满足条件的逆序对为：`(8,3)`, `(8,1)`, `(8,2)`, `(3,1)`, `(5,1)`, `(5,2)`，共 `6` 对。
- 思路：
	- 基于**归并排序的分治框架**，在合并两个有序子数组的过程中，利用**双指针滑动窗口**高效统计满足条件的逆序对。具体步骤如下：
	1. **递归划分**：将数组递归分割为子数组，直到子数组长度为 `1`。
	2. **合并与统计**：在合并两个已排序的子数组时，遍历左半部分的每个元素，通过滑动窗口找到右半部分中满足 `nums[i] > 2 * nums[j]` 的元素数量。
	3. **排序保证**：合并完成后，保证当前子数组有序，为后续递归合并提供正确输入。
+ 代码：

```cpp
class Solution {
    public:
        
        int merge(vector<int>& nums,int L,int M,int R)
        {
            vector<int> temp(R-L+1);
            int i = L,j = M+1,k = 0;
            int ans = 0;
            
            //计算左边数比右边数大2倍的数量
            int winR = M+1;
            for(int i=L;i<=M;i++)
            {
                while(winR<=R && nums[i]>(nums[winR]*2))
                    winR++;
                ans += winR - (M+1);
            }

            //排序合并
            i = L,j = M+1,k = 0;
            while(i<=M && j<=R)
            {
                temp[k++] = nums[i]<nums[j] ? nums[i++] : nums[j++];
            }
            //要么i越界了,把j剩下的拷贝进去
            while(j<=R)
                temp[k++] = nums[j++];
            //要么j越界了,把i剩下的拷贝进去
            while(i<=M)
                temp[k++] = nums[i++];
            for(int t=0;t<temp.size();t++)
                nums[L+t] = temp[t];//需要注意nums的区间
            return ans;
        }
        
        int process(vector<int>& nums,int L,int R)
        {
            int mid = L + (R - L)/2;
            int ans = 0;
            //递归边界条件
            if(L == R)
                return 0;
            ans = process(nums,L,mid) + process(nums,mid+1,R) + merge(nums,L,mid,R);
            return ans;
        }
    
        int Than2(vector<int>& nums) {
            int ans;
            ans = process(nums,0,nums.size()-1);
            return ans;
        }
    };
```

#### 区间和的个数问题
+ 区间和的个数问题：[327.区间和的个数](https://leetcode.cn/problems/count-of-range-sum/description/)
+ 思路：
	+ 转化为归并排序进行求解，复杂度为 $O(NlogN)$。将原问题转化为**前缀和数组**的有序对统计问题，区间和 `[i, j]` 对应前缀和 `sum[j] - sum[i]`，要求满足 `lower ≤ sum[j] - sum[i] ≤ upper`，即等价于统计满足 `sum[j] - upper ≤ sum[i] ≤ sum[j] - lower` 的有序对 `(i, j)`（`i < j`）。
+ 代码：

```cpp
class Solution {
    public:
        
        int merge(vector<long long>& sum,int L,int M,int R, int lower, int upper)
        {
            int ans = 0;
            int winL = L;
            int winR = L;
            for(int i=M+1;i<=R;i++)
            {
                long long min_ = sum[i] - upper;
                long long max_ = sum[i] - lower;
                while(winR<=M && sum[winR]<=max_)
                    winR++;
                while(winL<=M && sum[winL]<min_)
                    winL++;
                ans += winR - winL;
            }
            //做merge操作
            vector<long long> temp(R-L+1);
            int k=0;
            int i=L;
            int j=M+1;
            while(i<=M && j<=R)
                temp[k++] = sum[i]<sum[j] ? sum[i++] : sum[j++];
            while(i<=M)
                temp[k++] = sum[i++];
            while(j<=R)
                temp[k++] = sum[j++];
            for(int k=0;k<temp.size();k++)
                sum[L+k] = temp[k];
    
            return ans;
        }
    
        int process(vector<long long>& sum,int L,int R ,int lower, int upper)
        {
            int ans = 0;
            int mid = L + (R-L)/2;
            //递归边界条件
            if(L==R)
            {
                if(sum[L]>=lower && sum[L]<=upper)
                    return 1;
                else
                    return 0;
            }
            ans = process(sum,L,mid,lower,upper) 
            + process(sum,mid+1,R,lower,upper) 
            + merge(sum,L,mid,R,lower,upper);
            return ans;
        }
    
        int countRangeSum(vector<int>& nums, int lower, int upper) 
        {
            if(nums.size() == 0)
                return 0;
    
            //求前缀和数组
            vector<long long> sum;
            sum.push_back(nums[0]);
            for(int i=1;i<nums.size();i++)
            {
                sum.push_back(sum[i-1]+nums[i]);
            }
    
            int ans = 0;
            ans = process(sum,0,sum.size()-1,lower,upper);
            return ans;
        }
    };
```

### 快速排序
#### 颜色分类
+ 颜色分类问题：[75.颜色分类](https://leetcode.cn/problems/sort-colors/)
+ 思路：

![颜色分类问题](https://yugin-blog-1313489805.cos.ap-guangzhou.myqcloud.com/20250420144955.png)

+ 代码：

```cpp
class Solution {
public:
    void swap(vector<int>& nums,int a,int b)
    {
        int temp = nums[a];
        nums[a] = nums[b];
        nums[b] = temp;
    }

    void sortColors(vector<int>& nums) {
        if(nums.size()==1)
            return;
        int pre = -1;//数组的前一个区域
        int now = 0;
        int last = nums.size();//数组后的一个数

        while(now<last)
        {
            if(nums[now]<1)
            {
                swap(nums,now,pre+1);
                pre++;
                now++;
            }
            else if(nums[now]>1)
            {
                swap(nums,now,last-1);
                last--;
            }
            else
                now++;
        }
    }
};
```

#### 随机快排
+ 思路：
	+ 在前面所述的颜色分类问题的基础上，随机快排的 `partition()` 函数将数组分为 `<基准值区域`、`=基准值区域`、`>基准值区域` 三个区域，通过 `pre` 和 `last` 标记边界；
	+ 快速排序随机选择从 `[L, R-1]` 范围上选择基准值与 `nums[R]` 进行交换，在代码中通过 `swap(nums,R,rand()%(R-L+1))` 体现；
	+ 随机快排采用递归的方式完成对数组的排序，需要注意随机快排依赖 `qickSort()` 函数中的**递归终止条件**（`L >= R`）。
+ 代码：

```cpp
class Solution {
    public:
	    //交换函数
        void swap(vector<int>& nums,int a,int b)
        {
            int temp = nums[a];
            nums[a] = nums[b];
            nums[b] = temp;
        }
        
	    //荷兰国旗问题（颜色分类问题）的变形
        vector<int> partition(vector<int>& nums,int L,int R)
        {
            vector<int> area(2);
            int pre = L-1; // <区
            int last = R; // >区
            int now = L;
            if(L==R)
            {
                area[0] = L;
                area[1] = R;
                return area;
            }
            else
            {
                while(now<last)
                {
                    if(nums[now]==nums[R])
                        now++;
                    else if(nums[now]<nums[R])
                    {
                        swap(nums,now,pre+1);
                        pre++;
                        now++;
                    }
                    else
                    {
                        swap(nums,now,last-1);
                        last--;
                    }
                }
                swap(nums,last,R);//最后把nums[R]放到=区的末尾
                area[0] = pre+1;
                area[1] = last;
                return area;
            }
        }

		//快排递归函数（注意递归边界）
        void qickSort(vector<int>& nums,int L,int R)
        {
            //递归边界（注意）
            if(L>=R)
                return;

            //随机选择一个数和nums[R]进行交换
            srand(time(NULL));
            swap(nums,R,L+rand()%(R-L+1));

            vector<int> area(2);
            area = partition(nums,L,R);
            qickSort(nums,L,area[0]-1);
            qickSort(nums,area[1]+1,R);
        }
    
        vector<int> sortArray(vector<int>& nums) {
            qickSort(nums,0,nums.size()-1);
            return nums;
        }
    };
```

+ 分析：
	+ 快排的空间复杂度是 $O(logn)$，并且快排算法是不稳定的。