# DP
最优子问题, 找 状态转移方程 进行子问题扩大 直到 原问题

思路: 1. 发现递归 -> 2. 从上到下的递归 -> 3. 加入 记忆 的递归 -> 4. 递归 转 迭代 + 记忆-> 5. 优化记忆空间

一般来说, 还是 直接 找 状态转移方程 直接写实在

例题: 打家劫舍1
### 1. 发现递归: 是 **抢 i-1 家** 还是 **抢 i 和 i-2家** 赚
### 2. 从上到下递归:
```java
public int rob(int[] nums) {
    return rob(nums, nums.length-1);
}
private int rob(int[] nums, int i){
    if(i < 0)   return 0;
    return Math.max(rob(nums, i-1), rob(nums, i-2) + nums[i]);
}
```
### 3. 加入 记忆 的递归
```java
int[] memo;
public int rob(int[] nums) {
    memo = new int[nums.length + 1];
    return rob(nums, nums.length-1);
}
private int rob(int[] nums, int i){
    if(i < 0)   return 0;
    if(memo[i] != 0)    return memo[i];
    int res = Math.max(rob(nums, i-1), rob(nums, i-2) + nums[i]);
    memo[i] = res;
    return res;
}
```
### 4. 递归 转 迭代 + 记忆
```java
public int rob(int[] nums) {
    if (nums.length == 0) return 0;
    int[] memo = new int[nums.length + 1];
    memo[0] = 0;
    memo[1] = nums[0];
    for (int i = 1; i < nums.length; i++) {
        int val = nums[i];
        memo[i+1] = Math.max(memo[i], memo[i-1] + val);
    }
    return memo[nums.length];
}
```
### 5. 优化记忆空间
```java
public int rob(int[] nums) {
    if (nums.length == 0) return 0;
    int prev1 = 0;
    int prev2 = 0;
    for (int num : nums) {
        int tmp = prev1;
        prev1 = Math.max(prev2 + num, prev1);
        prev2 = tmp;
    }
    return prev1;
}
```

## 打家劫舍系列
隔一个抢一个,不能连着抢

子问题: 是 **抢 i-1 家** 还是 **抢 i 和 i-2家** 赚

转移方程: **dp[i] = max(dp[i-1], dp[i-2] + val);**

House Robber: https://leetcode.com/problems/house-robber/
```java
public int rob(int[] nums) {
    if(nums.length == 0)    return 0;
    if(nums.length == 1)    return nums[0];
    if(nums.length == 2)    return Math.max(nums[1], nums[0]);
    
    int[] dp = new int[nums.length];
    dp[0] = nums[0];
    dp[1] = Math.max(nums[1], nums[0]);
    
    for(int i = 2; i < nums.length; i++){
        dp[i] = Math.max(dp[i-1], dp[i-2] + nums[i]);
    }
    
    return dp[nums.length-1];
}
```

Delete and Earn: https://leetcode.com/problems/delete-and-earn/
```java
public int deleteAndEarn(int[] nums) {
   int[] sum = new int[10001];
   for(int num : nums){
       sum[num] += num;
   }
   
   int prev = 0, curr = 0;
   for(int i = 0; i < sum.length; i++){
       int temp = curr;
       curr = Math.max(curr, prev + sum[i]);
       prev = temp;
   }
   return curr;
}
```

House Robber II: https://leetcode.com/problems/house-robber-ii/

依然是相同的转移函数, 但是房子是个圈, 所以使用 分情况讨论: 0到N-1 和 1到N, 求最大

```java
public int rob(int[] nums) {
    // 2 method: 0 -> N-1, 1 -> N
    if(nums.length == 0)    return 0;
    if(nums.length == 1)    return nums[0];
    
    // 0 -> N-1
    int prev1 = 0, curr1 = 0;
    for(int i = 0; i < nums.length - 1; i++){
        int temp = curr1;
        curr1 = Math.max(curr1, prev1 + nums[i]);
        prev1 = temp;
    }
    
    // 1 -> N
    int prev2 = 0, curr2 = 0;
    for(int i = 1; i < nums.length; i++){
        int temp = curr2;
        curr2 = Math.max(curr2, prev2 + nums[i]);
        prev2 = temp;
    }
    
    return Math.max(curr1, curr2);
}
```


## DP 教程系列

Fibonacci Number: https://leetcode.com/problems/fibonacci-number/

转移函数: F(0) = 0; F(1) = 1; F(n) = F(n - 1) + F(n - 2), for n > 1.
```java
public int fib(int n) {
  if(n <= 1)  return n;
  int prev = 0, curr = 1;
  for(int i = 2; i <= n; i++){
      int temp = curr;
      curr = curr + prev;
      prev = temp;
  }
  return curr;
}
```
N-th Tribonacci Number: https://leetcode.com/problems/n-th-tribonacci-number/

转移函数: $T_0 = 0, T_1 = 1, T_2 = 1, and T_{n+3} = T_n + T_{n+1} + T_{n+2}$ for n >= 0.
```java
public int tribonacci(int n) {
  if(n <= 2)    return n < 1 ? 0 : 1;
  int prev2 = 0, prev1 = 1, curr = 1;
  for(int i = 3; i <= n; i++){
      int temp = curr;
      curr = curr + prev1 + prev2;
      prev2 = prev1;
      prev1 = temp;
      
  }
  return curr;
}
```

Climbing Stairs: https://leetcode.com/problems/climbing-stairs/

转移函数: $F[i] = F[i-1] + F[i-2]$  同 fibonacci转移

```java
public int climbStairs(int n) {
    int[] dp = new int[n+1];
    dp[0] = 1;
    dp[1] = 1;
    for(int i = 1; i < n; i++){
        dp[i+1] = dp[i] + dp[i-1];
    }
    return dp[n];
}
```

Decode Ways: https://leetcode.com/problems/decode-ways/

条件复杂一些的 climbing stairs, 根据前两位判断作转移, dp[i] = dp[i-1] + dp[i-2]

在当前数字为 0-9 , dp[i] += dp[i-1]; 当前两个数字为 10-26, dp[i] += dp[i-2]

```java
public int numDecodings(String s) {
    char[] chars = s.toCharArray();
    int[] dp = new int[chars.length + 1];
    dp[0] = 1;
    dp[1] = chars[0] == '0' ? 0 : 1;
    for(int i = 2; i <= chars.length; i++){
        int unitDigit = chars[i-1] - '0';
        int tenDigit = chars[i-2] - '0';
        if(unitDigit >= 1 && unitDigit <= 9){
            dp[i] += dp[i-1];
        }
        int tens = 10 * tenDigit + unitDigit;
        if(tens >= 10 && tens <= 26){
            dp[i] += dp[i-2];
        }
    }
    return dp[chars.length];
}
```

Min Cost Climbing Stairs: https://leetcode.com/problems/min-cost-climbing-stairs/

转移方程: $F[i] = min(F[i-1], F[i-2]) + cost[i]$
, 因为最后两个状态都可以结束, 使用min选择后两状态中最小值
```java
public int minCostClimbingStairs(int[] cost) {
    int[] dp = new int[cost.length];
    dp[0] = cost[0];
    dp[1] = cost[1];
    for(int i = 2; i < cost.length; i++){
        dp[i] = Math.min(dp[i-2], dp[i - 1]) + cost[i];
    }
    return Math.min(dp[cost.length - 1], dp[cost.length - 2]);
}
```

Jump Game: https://leetcode.com/problems/jump-game/

转移函数: $F[i] = true$ if $F[j] == true,F[j] + j >= i$ for $j < i$
```java
public boolean canJump(int[] nums) {
    int n = nums.length;
    if(n <= 1)  return true;
    if(nums[0] == 0)return false; 
    
    boolean[] dp = new boolean[n];
    dp[0] = true;
    
    for(int i = 1; i < n; i++){
        for(int j = i-1; j >= 0; j--){
            if(dp[j] && nums[j] + j >= i){
                dp[i] = true;
                break;
            }
        }            
    }
    return dp[n-1];
}
```
解法二: 转移函数: $far = max(far, N[i] + i)$
```java
public boolean canJump(int[] nums) {
    int i = 0, reach = 0;
    for(; i < nums.length && i <= reach; i++){
        reach = Math.max(reach, nums[i] + i);
    }
    return reach >= nums.length - 1;
}
```

Jump Game II: https://leetcode.com/problems/jump-game-ii/

转移函数: $dp[j] = min(dp[i] + 1, dp[j])$ where $j = i+1$ 且 $j < N[i] + i + 1$

```java
public int jump(int[] nums) {
    int n = nums.length;
    int[] dp = new int[n];
    Arrays.fill(dp, Integer.MAX_VALUE);
    dp[0] = 0;
    
    for(int i = 0; i < n; i++){
        for(int j = i + 1; j < nums[i] + i + 1 && j < n; j++){
            dp[j] = Math.min(dp[i] + 1, dp[j]);
        }
    }
    return dp[n-1];
}
```
解法二: 记录当前结束点 和 有效范围内的最远点, 抵达最远点, 更新
```java
public int jump(int[] nums) {
    int j = 0, curEnd = 0, farest = 0;
    for(int i = 0; i < nums.length - 1; i++){
        farest = Math.max(farest, nums[i] + i);
        if(i == curEnd){
            j++;
            curEnd = farest;
        }
    }
    return j;
}
```

Maximum Subarray: https://leetcode.com/problems/maximum-subarray/

转移函数: $dp[i] = max(dp[i-1] + nums[i], nums[i]);$    同时使用res来保存到每一步为止的最大值

```java
public int maxSubArray(int[] nums) {
    int[] dp = new int[nums.length];    
    dp[0] = nums[0];
    int res = nums[0];
    for(int i = 1; i < nums.length; i++){
        dp[i] = Math.max(dp[i-1]+nums[i], nums[i]);
        res = Math.max(res, dp[i]);
    }
    return res;
}
```

Maximum Sum Circular Subarray: https://leetcode.com/problems/maximum-sum-circular-subarray/

和上一题相比, 使用环形数组, 逆向思维: total - 最小子数组和 就能得到最大子数组和
![https://assets.leetcode.com/users/motorix/image_1538888300.png](https://assets.leetcode.com/users/motorix/image_1538888300.png)
```java
public int maxSubarraySumCircular(int[] nums) {
    int posSum = 0, negSum = 0, sum = 0;
    int posRes = nums[0], negRes = nums[0];
    for(int i = 0; i < nums.length; i++){
        posSum = Math.max(posSum + nums[i], nums[i]);
        posRes = Math.max(posRes, posSum);
        negSum = Math.min(negSum + nums[i], nums[i]);
        negRes = Math.min(negRes, negSum);
        sum += nums[i];
    }
    return posRes > 0 ? Math.max(posRes, sum - negRes): posRes;
}
```

Maximum Product Subarray: https://leetcode.com/problems/maximum-product-subarray/

使用 前缀积 和 后缀积, 进行双向遍历, 比较大小得到结果. 原理: 假设现在子数组范围在$A[i,j]$, 如果$A[i-1]$或者$A[j-1]$为positive, 则单向扩展; 如果两者都是negative, 则双向同时扩展; 最终要么到达 head, 要么到达 tail, 双向遍历即可.

```java
public int maxProduct(int[] nums) {
    int res = nums[0], l = 0, r = 0;
    for(int i = 0; i < nums.length; i++){
        l = (l == 0 ? 1 : l) * nums[i];
        r = (r == 0 ? 1 : r) * nums[nums.length - i - 1];
        res = Math.max(res, Math.max(l , r));
    }
    return res;
}
```

Maximum Length of Subarray With Positive Product: https://leetcode.com/problems/maximum-length-of-subarray-with-positive-product/

使用 pos 和 neg 记录长度, pos代表乘积为正的当前长度, neg代表乘积为负的当前长度: 1. 当前值 == 0, 清空; 2. 当前数值 > 0, pos+1,neg根据情况判断是否 +1; 3. 当前值 < 0, 交换 pos 和 neg 的值

```java
public int getMaxLen(int[] nums) {
    int pos = 0, neg = 0, res = 0;
    for(int num : nums){
        if(num == 0){
            pos = 0;
            neg = 0;
        }else if(num > 0){
            pos += 1;
            neg = neg == 0 ? 0 : neg + 1;
        }else{
            int temp = pos;
            pos = neg == 0 ? 0 : neg + 1;
            neg = temp + 1;
        }
        res = Math.max(res, pos);
    }
    return res;
}
```

Best Sightseeing Pair: https://leetcode.com/problems/best-sightseeing-pair/

原本的转移方程已给出: $values[i] + values[j] + i - j$, 可以拆分并更改为: 求 $values[i] + i$ 和 $values[j] - j$ 两个数的最大和, 使用left记录$values[i] + i$的最大值, 并用res 记录和的最大值

```java
public int maxScoreSightseeingPair(int[] values) {
    if(values.length == 0)  return 0;
    int res = 0, left = values[0];
    for(int i = 1; i < values.length; i++){
        res = Math.max(res, left + values[i] - i);
        left = Math.max(left, values[i] + i);
    }
    return res;
}
```

Coin Change: https://leetcode.com/problems/coin-change/

记录到 当前总价 的最小硬币数即可, dp[i] = min(dp[i], dp[i - coins[j]] + 1)
```java
public int coinChange(int[] coins, int a) {
    int n = coins.length;
    int[] dp = new int[a+1];
    Arrays.fill(dp, a+1);
    dp[0] = 0;
    for(int i = 1; i <= a; i++){
        for(int j = 0; j < n; j++){
            if(coins[j] <= i){
                dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);
            }
        }
    }
    return dp[a] > a ? -1: dp[a];
}
```

## 背包问题主要思路: 遍历每个物品, 在能装下物品的前提下(常使用反向遍历), 进行状态转移(dp[i] = max(dp[i], 1+dp[i-k]))

Coin Change 2: https://leetcode.com/problems/coin-change-2/

标准背包问题DP, DP记录的是到当前coin种类下各个价格时的组合数量, 使用DP记录使用到上一种coin为止的各种coins, 到各个价格时候的组合数, 在至少可以使用一种新coin的情况下 (j=coins[i]), dp[j] += dp[j - coins[i]] 得到总价为 j 时, 使用新coin参与的组合数量. 对于重复使用问题, 如3+3=6时, dp[3] += dp[0], dp[6] += dp[3]解决

```java
public int change(int amount, int[] coins) {
    int[] dp = new int[amount+1];
    dp[0] = 1;
    for(int i = 0; i < coins.length; i++){
        for(int j = coins[i]; j <= amount; j++){
            dp[j] += dp[j - coins[i]];
        }
    }
    return dp[amount];
}
```

474. Ones and Zeroes: https://leetcode.com/problems/ones-and-zeroes/

背包问题带提前优化, 长度短的优先使用, 构建pairs储存基本信息, 使用背包思想DP

遍历每个物品, 在能装下的前提下, 如: for(int j = m; j >= pair[0]; j--) 和 for(int k = n; k >= pair[1]; k--), 转移方程:

dp[j][k] = Math.max(dp[j][k], 1 + dp[j - pair[0]][k - pair[1]]);
```java
public int findMaxForm(String[] strs, int m, int n) {
    // preparation
    int[][] pairs = new int[strs.length][2];
    Arrays.sort(strs, (s1, s2) -> s2.length() - s1.length());
    for(int i = 0; i < strs.length; i++){
        String s = strs[i];
        int oneS = 0, zeroS = 0;
        for(int j = 0; j < s.length(); j++){
            if(s.charAt(j) == '1') oneS += 1; else zeroS += 1;
        }
        pairs[i][0] = zeroS; pairs[i][1] = oneS;
    }
    // memo
    int[][] dp = new int[m+1][n+1];
    // DP
    for(int i = 0; i < pairs.length; i++){
        int[] pair = pairs[i];
        for(int j = m; j >= pair[0]; j--){
            for(int k = n; k >= pair[1]; k--){
                dp[j][k] = Math.max(dp[j][k], 1 + dp[j - pair[0]][k - pair[1]]);
            }
        }
    }
    return dp[m][n];
}
```

983. Minimum Cost For Tickets: https://leetcode.com/problems/minimum-cost-for-tickets/

强化版 coin change, 依然使用 i 从 0 到 最大值的每个中间状态进行DP, 注意三点: 

1. 最大值不再是days.length, 而是 max(days)
2. 注意使用 set 保存所有有效日期, 对于无效日期, 继承上一时刻状态 dp[i] = dp[i-1], 只针对有效日期进行dp
3. 转移内部注意 max(0, i-dur[j]), 确保所有有效日期都能访问dp[0], 不使用 if(i-dur[j] >= 0) 原因: 1,6,16 时, 确保能访问 7-days pass, 而不是跳过

转移方程: dp[i] = Math.min(dp[i], dp[Math.max(0, i - dur[j])] + costs[j]);

```java
public int mincostTickets(int[] days, int[] costs) {
    Set<Integer> set = new HashSet<>();
    for(int day : days) set.add(day);
    int n = days[days.length - 1];
    int[] dp = new int[n+1];
    int[] dur = new int[]{1,7,30};
    Arrays.fill(dp, Integer.MAX_VALUE);
    dp[0] = 0;
    
    for(int i = 1; i <= n; i++){    // 到第N天的最小cost
        if(set.contains(i)){
            for(int j = 0; j < dur.length; j++){
                dp[i] = Math.min(dp[i], dp[Math.max(0, i - dur[j])] + costs[j]);
            }
        }else{
            dp[i] = dp[i-1];
        }
    }
    return dp[dp.length - 1];
}
```

## 买卖股票

Best Time to Buy and Sell Stock: https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

转移方程: res = max(res, nums[i] - min); min = min(min, nums[i]);

```java
public int maxProfit(int[] prices) {
    int min = prices[0], res = 0;
    for(int i = 1; i < prices.length; i++){
        res = Math.max(res, prices[i] - min);
        min = Math.min(min, prices[i]);
    }
    return res;
}
```

Best Time to Buy and Sell Stock II: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/

将两次离散的买卖股票的时机拆成连续的时间, 即每天都在买卖股票直到最高价当日. 即第二天比前一天贵, 则昨天买,今天卖

```java
public int maxProfit(int[] prices) {
    int n = prices.length;
    int res = 0, hold = prices[0];
    for(int i = 1; i < prices.length; i++){
        res = prices[i] > hold ? res + prices[i] - hold : res;
        hold = prices[i];
    }
    return res;
}
```

Best Time to Buy and Sell Stock with Cooldown: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/

增加的冷却时间, 使用状态机办法解决, 持股A, 空仓C, 歇逼B 共三种状态. 再三种状态中转移, 持股A -> 持股A/歇逼B, 空仓C -> 空仓C/持股A, 歇逼B -> 空仓C. 返回 空仓C 和 歇逼B 的最大值.
![https://raw.githubusercontent.com/hot13399/leetcode-graphic-answer/master/Best%20Time%20to%20Buy%20and%20Sell%20Stock%20with%20Cooldown.jpg](https://raw.githubusercontent.com/hot13399/leetcode-graphic-answer/master/Best%20Time%20to%20Buy%20and%20Sell%20Stock%20with%20Cooldown.jpg)

```java
public int maxProfit(int[] prices) {
    int s0 = 0, s1 = -prices[0], s2 = 0;
    for(int i = 1; i < prices.length; i++){
        int lastS2 = s2;
        s2 = s1 + prices[i]; // 交易后, 空仓的收益
        s1 = Math.max(s0 - prices[i], s1);    // 买入的收益
        // 使用max来确保可以一直歇逼
        s0 = Math.max(lastS2, s0);  // 歇逼的收益
    }
    return Math.max(s0, s2);
}
```

Longest Increasing Subsequence: https://leetcode.com/problems/longest-increasing-subsequence/

使用一维DP, 记录到 i 为止的单增子序列长度, 从0开始遍历end, 和 end 之前所有的lastEnd相比, 来更新dp[end]; 如果当前值大于lastEnd的值, 则lastEnd的长度+1和当前最大值. 转移函数:

dp[end] = max(dp[end], dp[lastEnd] + 1) if num[end] > num[lastEnd]

```java
public int lengthOfLIS(int[] nums) {
    int n = nums.length;
    int[] dp = new int[n];
    dp[0] = 1;
    int res = 1;
    
    for(int end = 0; end < n; end++){
        dp[end] = 1;
        for(int lastEnd = 0; lastEnd < end; lastEnd++){
            if(nums[lastEnd] < nums[end]){
                dp[end] = Math.max(dp[end], dp[lastEnd] + 1);
            }
        }
        res = Math.max(res, dp[end]);
    }
    return res;
}
```


Longest Palindromic Substring: https://leetcode.com/problems/longest-palindromic-substring/

重新构建string时, len+1 原因是len最小为0时, 实际长度为1, 转移方程: 

1. dp[start][end] = true if len == 0
2. dp[start][end] = chars[start] == chars[end] if len == 1
3. dp[start][end] = chars[start] == chars[end] && dp[start+1][end-1] if len > 1

解法2: 顶级解法 manacher 
https://www.acwing.com/file_system/file/content/whole/index/content/446985/
1. 在每个char前和结尾处插入 特殊字符, 将 N 变为 2N+1 的奇数字符串
2. 使用半径rad作为快速搜索和扩展的trick, 记录 最右回文串的 圆心 id 和 半径 right
3. 如果 i < right, 半径 rad 寻找存在加速可能: 使用对称性, 在 id 左侧找到 和 i - id == id - j 一样的 j, j = 2 * id - i. 如果以 j 为中心的 rad 超过了 right - id, 则需要取较小值, 因为在右侧相应的 i 位置没有验证: 
Math.min(right - i, rad[2 * id - i])
4. 暴力枚举推出 以 i 为中心的新 rad, 更新 right, id, rad[i]即可
```java
public String longestPalindrome(String s) {
    char[] chars = s.toCharArray();
    int n = s.length();
    String res = "";
    boolean[][] dp = new boolean[n][n];
    
    for(int len = 0; len < n; len++){
        for(int start = 0; len + start < n; start++){
            int end = start + len;
            if(len == 0){
                dp[start][end] = true;
            }else if(len == 1){
                dp[start][end] = chars[start] == chars[end];
            }else {
                dp[start][end] = (chars[start] == chars[end] && dp[start + 1][end - 1]);
            }
            if(dp[start][end] && len + 1 > res.length()){
                res = s.substring(start, end+1);
            }
        }
    }
    
    return res;
}

 public String longestPalindrome(String s) {
     // Manacher algo
     // 1.add specfic char
     StringBuilder sb = new StringBuilder();
     sb.append('#');
     for(char c : s.toCharArray()){
         sb.append(c);
         sb.append('#');
     }
     
     System.out.println(sb.toString());
     
     // 2. features
     int[] rad = new int[sb.length()];   // rad at each index
     int id = -1;    // rightmost palindrome index
     int right = -1; // rightmost palindrome right bound
     int max = 1, idx = 0;
     
     // 3. simulate
     for(int i = 0; i < sb.length(); i++){
         rad[i] = right > i ? Math.min(right - i, rad[2 * id - i]) : 1;
         while(i - rad[i] >= 0 && i + rad[i] < sb.length() 
               && sb.charAt(i+rad[i]) == sb.charAt(i-rad[i])){
             rad[i]++;
         }
         
         // update rightmost
         if(i + rad[i] > right){
             right = i + rad[i];
             id = i;
         }
         
         // update max
         if(rad[i] > max){
             max = rad[i]; 
             idx = i;
         }
     }
     
     System.out.println("rad: " + max);
     System.out.println("idx: " + idx);
     
     // rebuild 
     StringBuilder res = new StringBuilder();
     for(int i = idx - max + 1; i < idx + max - 1; i++){
         if(sb.charAt(i) != '#'){
             res.append(sb.charAt(i));
         }
     }
     
     return res.toString();
 }
```

## 排列组合DP

Count All Valid Pickup and Delivery Options: https://leetcode.com/problems/count-all-valid-pickup-and-delivery-options/

将两种插入方式相加:
1. P和D一起添加到了序列中连续的位置。由于序列的长度为 (i - 1) * 2，那么可以添加的位置为序列的长度加 1，即 2(i - 1) + 1 = 2i - 12(i−1)+1=2i−1；
2. P和D被添加到了序列中不连续的位置，那么就相当于在 i * 2 - 1 个位置中选择两个不同的位置，前者用作添加P, 后者加D, 方法数为组合数 $\binom{2i - 1}{2} = (2i−1)(i−1)$

相加 = (2 * i - 1) * i , 转移方程: $f[i] = (2*i - 1) * i * f[i-1];$

```java
public int countOrders(int n) {
  if(n == 1)  return 1;
  long ans = 1, mod = 1000000007;
  for(int i = 2; i <= n; i++){
      ans = (ans * (2 * i - 1) * i) % mod;
  }
  return (int) ans;
}
```


### Greedy DP
Minimum Domino Rotations For Equal Row: https://leetcode.com/problems/minimum-domino-rotations-for-equal-row/

记录每个数字出现次数和相同时的出现次数, 遍历所有数字, 尝试交换: topSet[i] + botSet[i] - SameSet[i] == len 则满足交换条件, 返回最小的交换次数

```java
 public int minDominoRotations(int[] tops, int[] bottoms) {
     int[] topSet = new int[7];
     int[] botSet = new int[7];
     int[] sameSet = new int[7];
     int n = tops.length;
     
     for(int i = 0; i < n; i++){
         topSet[tops[i]] += 1;
         botSet[bottoms[i]] += 1;
         if(tops[i] == bottoms[i])   sameSet[tops[i]] += 1;
     }
     
     for(int i = 1; i < 7; i++){
         if(topSet[i] + botSet[i] - sameSet[i] == n){
             return n - Math.max(topSet[i], botSet[i]);
         }
     }
     return -1;
 }
```

## 2304. Minimum Path Cost in a Grid
https://leetcode.com/problems/minimum-path-cost-in-a-grid/

二维DP, 但是三维遍历, 本来思考DFS, 但是需要memory, 直接转DP, 转移方程在DFS中推导: 

mem[i+1][k] = Math.min(mem[i+1][k], mem[i][j] + nextRow[k] + grid[i+1][k])

```java
 public int minPathCost(int[][] grid, int[][] moveCost) {
     int n = grid.length;
     if(n == 0 || grid == null || moveCost.length == 0 || moveCost == null)  return 0;
     int m = grid[0].length;
     int res = Integer.MAX_VALUE;
     
     int[][] mem = new int[n + 1][m + 1];
     for(int j = 0; j < m; j++)  mem[0][j] = grid[0][j];
     for(int i = 1; i < n; i++)  Arrays.fill(mem[i], Integer.MAX_VALUE);
     
     for(int i = 0; i < n - 1; i++){
         for(int j = 0; j < m; j++){
             int[] nextRow = moveCost[grid[i][j]];
             for(int k = 0; k < m; k++){
                 mem[i+1][k] = Math.min(mem[i+1][k], mem[i][j] + nextRow[k] + grid[i+1][k]);
             }
         }
     }
     for(int j = 0; j < m; j++)  res = Math.min(res, mem[n-1][j]);
     return res;
 }
```

## 120. Triangle
从上到下DP: dp[i+1][j] = Math.min(dp[i+1][j], dp[i][j] + t.get(i+1).get(j)) 和 dp[i+1][j+1] = Math.min(dp[i+1][j+1], dp[i][j] + t.get(i+1).get(j+1))

从下到上DP: t[i][j] += Math.min(t[i+1][j], t[t+1][j+1])

```java
 public int minimumTotal(List<List<Integer>> t) {
     int n = t.size();
     if(n == 0)  return 0;
     
     int m = t.get(n-1).size();
     // 1. up2botm, i - row index, j - index in row, O(mn)
     // dp[i+1][j] = Math.min(dp[i+1][j], dp[i][j] + t.get(i+1).get(j))
     // dp[i+1][j+1] = Math.min(dp[i+1][j+1], dp[i][j] + t.get(i+1).get(j+1))
     
     int[][] dp = new int[n][m+1];
     for(int[] row : dp) Arrays.fill(row, Integer.MAX_VALUE);
     dp[0][0] = t.get(0).get(0);
     
     for(int i = 0; i < n - 1; i++){
         for(int j = 0; j < i + 1; j++){
             dp[i+1][j] = Math.min(dp[i+1][j], dp[i][j] + t.get(i+1).get(j));
             dp[i+1][j+1] = Math.min(dp[i+1][j+1], dp[i][j] + t.get(i+1).get(j+1));
         }
     }
     
     int res = Integer.MAX_VALUE;
     for(int i = 0; i < m; i++)  res = Math.min(res, dp[n-1][i]);
     return res;
     
     // 2. botm-up, O(n)
     // t[i][j] += Math.min(t[i+1][j], t[t+1][j+1])
     for(int i = t.size() - 2; i >= 0; i--){
         for(int j = 0; j < t.get(i).size(); j++){
            t.get(i).set(j, t.get(i).get(j) + Math.min(t.get(i+1).get(j), t.get(i+1).get(j+1))); 
         }
     }
     return t.get(0).get(0);
 }
```
# Unique Paths 系列
## 62. Unique Paths
https://leetcode.com/problems/unique-paths/

dp[i][j] = = dp[i-1][j] + dp[i][j-1]

```java
 public int uniquePaths(int m, int n) {
     int[][] dp = new int[m+1][n+1];
     Arrays.fill(dp[0], 1);
     for(int i = 0; i < m; i++)  dp[i][0] = 1;
     
     for(int i = 1; i < m; i++){
         for(int j = 1; j < n; j++){
             dp[i][j] = dp[i-1][j] + dp[i][j-1];
         }
     }
     return dp[m-1][n-1];
 }
```
63. Unique Paths II
https://leetcode.com/problems/unique-paths-ii/

带障碍, 只需要避开障碍即可, 障碍 = 0
```java
 public int uniquePathsWithObstacles(int[][] o) {
     int n = o.length;
     if(n == 0 || o == null) return 0;
     
     int m = o[0].length;
     int[][] dp = new int[n+1][m+1];
     // path in 0-row
     for(int i = 0; i < m; i++){
         if(o[0][i] == 0){
             dp[0][i] = 1;
         }else break;
     }
     
     // path in 0-col
     for(int i = 0; i < n; i++){
         if(o[i][0] == 0){
             dp[i][0] = 1;
         }else break;
     }
     
     for(int i = 1; i < n; i++){
         for(int j = 1; j < m; j++){
             if(o[i][j] == 0){
                 dp[i][j] = dp[i-1][j] + dp[i][j-1];
             }
         }
     }
     return dp[n-1][m-1];
 }
```

## 343. Integer Break
https://leetcode.com/problems/integer-break/

跟据 长度 i 和 分的段数 j 进行DP,  j * (i-j) 是直接分成两段 i-j 和 j, 作为base case; dp[j] * (i-j) 分为 dp[j] 和 i-j , 是继承 dp[j] 

```java
public int integerBreak(int n) {
    int[] dp = new int[n+1];
    for(int i = 2; i <= n; i++){
        for(int j = 1; j < i; j++){
            // j * (i-j) 分为两段, dp[j] * (i-j), i-j 乘上 j 之前的累计结果
            dp[i] = Math.max(dp[i], Math.max(j * (i-j), dp[j] * (i-j)));
        }
    }
    return dp[n];
}
```

## 2311. Longest Binary Subsequence Less Than or Equal to K
https://leetcode.com/problems/longest-binary-subsequence-less-than-or-equal-to-k/

依然看到子序列, 考虑DP. 子序列DP一般都考虑 length 入手, 即 长度为 i 的情况下最优子状态.    
1. 在 bit manuiplation 中, 左移一位 = ori * 2, 所以使用 dp[i] * 2 相当于左移一位, 构建新数字:    
   dp[i] * 2 + c - '0' 
2. 遍历 并 查询每个 char 构建的新数字 是否 小于等于 k, 构建新数字的办法就是1; 使用 j 记录 seq长度, 当新数字有效, 更新 j; 每次访问 char, 就更新所有到当前长度的最小值.

```java
public int longestSubsequence(String s, int k) {
    // dp[j+1] = dp[j] * 2 + c -'0'; 构建小于k的转移
    // dp[i] = Math.min(dp[i], dp[i-1] * 2 + c -'0';); i-1是长度-1的seq最小值
    int[] dp = new int[s.length() + 1]; // 存 长度为 i 的最小seq值
    int j = 0;
    for(char c : s.toCharArray()){
        if(dp[j] * 2 + c -'0' <= k){    // 左移一位 = num * 2
            dp[j+1] = dp[j] * 2 + c -'0';
            j++;    // 有效长度 + 1
        }
        // 更新到当前长度为止, 所有长度的最小值
        for(int i = j; i > 0; i--){
            dp[i] = Math.min(dp[i], dp[i-1] * 2 + c -'0');
        }
    }
    return j;
}
```