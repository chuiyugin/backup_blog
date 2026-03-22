---
title: 塔子哥算法经验分享
tags:
  - 算法学习
categories:
  - 算法学习
date: 2026-3-22 23:00:00
excerpt: C++、编程语言、算法学习
---

# 塔子哥算法经验分享
## 动态规划
### 0 - 1 背包问题
#### 理论基础
+ 理论基础详见：[0 - 1背包问题理论基础](https://chuiyugin.github.io/2025/03/07/zuo_algorithm_2/#%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92)
+ ⚠️注意区分以下两类问题：
#### 背包价值最大化（max）类→ 求可达性/最值
+  分割等和子集：[416.分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/description/)
+ 思路：
	+ 这道题目可以看成是 `0 - 1` 背包问题的变式，题目只要求将一个数组分割成两个子集，使得两个子集的元素和相等。那么可以将背包容量设定为数组总和 `sum` 的一半，即 `sum/2`，各个物品的重量和价值都是 `nums` 数组的元素，若能刚好装下 `sum/2` 容量的背包则返回 `true`，否则返回 `false`。
	+ 按照动态规划五部曲进行分析：
		+ 确定 `dp` 数组及下标的含义：`dp[j]` 的定义为：背包在容量为 `j` 的情况下从 `0` 到 `i` 的物品中选择装入得到的最大重量。 
		+ 确定递推公式（与 `0 - 1` 背包问题一致）：
			+ 状态转移方程 `dp[j] = max(dp[j], dp[j-nums[i]]+nums[i]);` ;
		+ `dp` 数组如何初始化：全部初始化成 `0`；
		+ 确定遍历顺序：在 `i` 上正序遍历，在 `j` 上倒序遍历；
		+ 举例推导 `dp` 数组符合要求。
+ 代码：

```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for(int i=0; i<nums.size(); i++){
            sum+=nums[i];
        }
        if(sum%2 == 1) // 求和之后是奇数
            return false;
        // 背包容量是 sum/2
        int target = sum/2;
        vector<int> dp(target+1, 0);
        for(int i=0; i<nums.size(); i++){
            for(int j=target; j>=nums[i]; j--){
                dp[j] = max(dp[j], dp[j-nums[i]]+nums[i]);
                if(dp[target] == target)
                    return true;
            }
        }
        return false; 
    }
};
```

#### 装满背包有多少种方案类 → 求方案数
* 整数划分(01背包基础题)：[整数划分(01背包基础题)](https://codefun2000.com/p/P3627)
+ 思路：
	+ 这题本质是在问：从 `1~n` 里选若干个互不相同的数，使它们的和等于 `n`，一共有多少种选法。由于每个数只能用一次，所以它就是一个 **0/1 背包计数问题**。
	+ 按照动态规划五部曲进行分析：
		+ 确定 `dp` 数组及下标的含义：从数字 `1 ~ i` 中选取若干个互不相同的正整数，使它们的和恰好为 `j` 的方案数。
		+ 确定递推公式（与 `0 - 1` 背包问题一致）：
			+ 状态转移方程 `dp[j] = (dp[j] + dp[j - i]) % MOD;` ;
		+ `dp` 数组如何初始化：`dp[0] = 1` 表示凑出和为 `0` 有 1 种方法，即“什么都不选”；其余位置初始化为 `0`，表示一开始还没有方案能凑出对应的和。
		+ 确定遍历顺序：在 `i` 上正序遍历，在 `j` 上倒序遍历；
		+ 举例推导 `dp` 数组符合要求。
+ 代码：

```cpp
#include <bits/stdc++.h>

using namespace std;

const int MOD = 1e9 + 7;

int main(){
    int num;
    scanf("%d", &num);
    // dp[j] 表示装满容量为j的背包有多少种方案
    vector<int> dp(num+1, 0);
    dp[0] = 1;
    for(int i=1; i<=num; i++){
        for(int j=num; j>=i; j--){
            dp[j] = (dp[j] + dp[j - i]) % MOD;
        }
    }
    printf("%d", dp[num]%MOD);

    return 0;
}
```
