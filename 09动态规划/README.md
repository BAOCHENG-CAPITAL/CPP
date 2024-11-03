# 动态规划

|                |              |                                                              |
| -------------- | ------------ | ------------------------------------------------------------ |
| 爬楼梯         | easy         | https://leetcode.cn/problems/climbing-stairs/description/    |
| 打家劫舍       | easy         | https://leetcode.cn/problems/house-robber/description/       |
| 最大子段和     | easy         | https://leetcode.cn/problems/maximum-subarray/description/   |
| 找零钱         | medium       | https://leetcode.cn/problems/coin-change/description/        |
| 三角形         | medium       | https://leetcode.cn/problems/maximum-subarray/description/   |
| 最长上升子序列 | medium, hard | https://leetcode.cn/problems/longest-increasing-subsequence/description/ |
| 最小路径和     | medium       | https://leetcode.cn/problems/minimum-path-sum/description/   |
| 地牢游戏       | hard         | https://leetcode.cn/problems/dungeon-game/description/       |

# 动态规划（Dynamic Programming）概述

动态规划（dynamic programming）是运筹学的一个分支，是求解决策过程最优化的数学方法。它是20世纪50年代初美国数学家R.E.Bellman等人提出的最优化原理，它利用各阶段之间的关系，逐个求解，最终求得全局最优解。在设计动态规划算法时，需要确认原问题与子问题、动态规划状态、边界状态结值、状态转移方程等关键要素。

在算法面试中，动态规划是最常考察的题型之一，大多数面试官都以能否较好的解决规划相关问题来区分候选人是否“聪明”。

# 动态规划原理

1. 确认原问题与子问题

   原问题为求n阶台阶所有走法的数量，子问题是求1阶台阶、2阶台阶、...、n-1阶台阶的走法。

2. 确认状态

   本体的动态规划状态单一，第i个状态即为i阶台阶的所有走法数量。

3. 确认边界状态的值

   边界状态为1阶台阶与2阶台阶的走法，1阶台阶有1种走法，2阶台阶有2种走法，即dp[1]=1，dp[2]=2。

4. 确定状态转移方程

   将求第i个状态的值转移为求第i-1个状态值与第i-2个状态的值，动态规划转移方程
   $$
   \text{dp}[i]=\text{dp}[i-1]+\text{dp}[i-2](i\geq 3)
   $$

# 爬楼梯

```c++
class Solution {
public:
    int climbStairs(int n) {
        std::vector<int>dp(n+3,0);
        dp[1]=1;
        dp[2]=2;
        for(int i=3;i<=n;i++){
            dp[i]=dp[i-1]+dp[i-2];
        }
        return dp[n];
    }
};
```

# 打家劫舍

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        if(nums.size()==0){
            return 0;
        }
        if(nums.size()==1){
            return nums[0];
        }//设第i个房间的最优解为dp[i]
        std::vector<int>dp(nums.size(),0);
        dp[0]=nums[0];
        dp[1]=std::max(nums[0],nums[1]);
        for(int i=2;i<nums.size();i++){
            dp[i]=std::max(dp[i-1],dp[i-2]+nums[i]);
        }
        return dp[nums.size()-1];
    }
};
```

