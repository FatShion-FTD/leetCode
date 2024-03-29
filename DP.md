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

# Jump Game 系列
## Jump Game
https://leetcode.com/problems/jump-game/

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

## Jump Game II
https://leetcode.com/problems/jump-game-ii/

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

## 1696. Jump Game VI
https://leetcode.com/problems/jump-game-vi/

DP + mono-queue, 核心在于使用单调队列维护最大值 且 及时更新.    
mono-queue 储存 index, queue的首 存储最大值, 尾存储最小值;    
MONO: 每次加入前, 去掉所有比 当前小的 值.    
判读是否越界, 拿 队首的 index, 和当前 index 比较即可.

```java
 public int maxResult(int[] nums, int k) {
     int n = nums.length;
     if(n == 0 || nums == null)  return 0;
     
     Deque<Integer> deque = new ArrayDeque<>();
     deque.offer(0);
     for(int i = 1; i < n; i++){
         nums[i] += nums[deque.peekFirst()];
         // make queue only maintain vals greater than nums[i]
         while(!deque.isEmpty() && nums[deque.peekLast()] <= nums[i])
             deque.pollLast();
         // add cur index to queue
         deque.offer(i);
         // if max val index is no longer valid, poll
         if(i - deque.peekFirst() >= k)
             deque.pollFirst();
     }
     return nums[n-1];
 }
```

## Maximum Subarray
https://leetcode.com/problems/maximum-subarray/

最基础的Kadane 算法, 使用DP的办法, 记录到 i 为止的子数组的最优子状态. 从 O(N^2) 变为 O(N)

转移函数: $dp[i] = max(dp[i-1] + nums[i], nums[i]);$    

同时使用res来保存到每一步为止的最大值

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

## 983. Minimum Cost For Tickets
https://leetcode.com/problems/minimum-cost-for-tickets/

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

# 买卖股票

## Best Time to Buy and Sell Stock
https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

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

## Best Time to Buy and Sell Stock II
https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/

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

## 123. Best Time to Buy and Sell Stock III
https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/

因为最多 2次 交易, 所以使用状态机, 4种状态: hold1, sell1, hold2, sell2. 每次都和自己比较, buy 维持最小, sell 维持最大. 在第二次交易时, 考虑第一次的利润, 所以需要 hold2 - sell1.

```java
class Solution {
    public int maxProfit(int[] prices) {
        // at most 2 transaction
        // hold only one stock at one time
        // 4 state: hold1, sell1, hold2, sell2
        int hold1 = Integer.MAX_VALUE, sell1 = 0, hold2 = Integer.MAX_VALUE, sell2 = 0;
        for (int price : prices) {
            hold1 = Math.min(hold1, price);                 // min first buy
            sell1 = Math.max(sell1, price - hold1);         // max firsy sell
            hold2 = Math.min(hold2, price - sell1);         // min second sell
            sell2 = Math.max(sell2, price - hold2);
        }
        return Math.max(0, Math.max(sell1, sell2));
    }
}
```

## 188. Best Time to Buy and Sell Stock IV
https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/

$$ dp[k][i] = max(dp[k][i-1], max(price[i] - price[j] + dp[k - 1][j])) $$

表示在 第 k 轮交易的 第 i 天, 两种操作: 
1. 不进行操作, 继承昨天状态 dp[k][i-1]; 
2. 进行卖出, 则 使用 price[i] - price[j] 获得利润, 并 + dp[k - 1][j], 加上上一轮交易在第 j 日产生的最大利润.

为使得 price[i] - price[j] + dp[k - 1][j]) 最大, 则要记录与 j 相关的信息: 维护最小值 prices[j] - dp[k - 1][j], 表示 第 j 日前总投入最小.

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        //dp[k][i], profit at k-th transaction, i-th price
        // dp[k][i] = max(dp[k][i-1], price[i] - min(price[j] - dp[k-1][j]))
        int n = prices.length, res = 0;        
        int[][] dp = new int[k + 1][n];
        if (prices == null || prices.length == 0)
            return 0;
        
        for (int t = 1; t <= k; t++) {
            int min = prices[0];                    
            for (int i = 1; i < n; i++) {
                min = Math.min(min, prices[i] - dp[t - 1][i]);
                dp[t][i] = Math.max(dp[t][i - 1], prices[i] - min);
                res = Math.max(res, dp[t][i]);
            }
        }
        return res;
    }
}
```

## Best Time to Buy and Sell Stock with Cooldown
 https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/

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

## Longest Increasing Subsequence
https://leetcode.com/problems/longest-increasing-subsequence/

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

## 2370. Longest Ideal Subsequence
https://leetcode.com/problems/longest-ideal-subsequence/

和 最长单增子序列很像, 同样使用 一个 dp 记录每个元素最后出现的时的 最长子序列, 在合法范围内, dp[i] = max(dp[i], dp[j] + 1), 本题的条件是, 在一个 +-k 的范围内波动. 

```java
 public int longestIdealString(String s, int k) {
     if (s == null || s.length() == 0)
         return 0;
     
     int n = s.length(), res = 0;
     int[] dp = new int[26];
     
     for (char c : s.toCharArray()) {
         int idx = c - 'a';
         int tmp = 1;
         for (int i = Math.max(0, idx - k); i <= Math.min(25, idx + k); i++) {
             tmp = Math.max(tmp, dp[i] + 1);
         }
         res = Math.max(res, dp[idx] = tmp);
     }

     return res;
 }
```

# Palindrome SubSeq/String
## 516. Longest Palindromic Subsequence
https://leetcode.com/problems/longest-palindromic-subsequence/

最长 palin 子序列. dp[i][j] 表示以 i 开头, j 结尾的子序列中的 palin 长度. 三种转移:
1. i 到 i, dp[i][i] = 1, 表示单个char, 是 palin
2. dp[i][j] = dp[i + 1][j - 1] + 2 当 arr[i] == arr[j]
3. dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]) 表示从 i-1 到 j 和 i 到 j+1 的长度中继承更长的 subseq.

```java
 public int longestPalindromeSubseq(String s) {
     if (s == null || s.length() == 0)
         return 0;
     
     int n = s.length(), res = 1;
     int[][] dp = new int[n + 1][n + 1];
     for (int i = n - 1; i >= 0; i--) {
         dp[i][i] = 1;
         for (int j = i + 1; j < n; j++) {
             if (s.charAt(i) == s.charAt(j)) {
                 dp[i][j] = dp[i + 1][j - 1] + 2;
             } else {
                 dp[i][j] = Math.max(dp[i + 1][j], dp[i][j - 1]);
             }
             res = Math.max(res, dp[i][j]);
         }
     }
     
     return res;
 }
```

## Longest Palindromic Substring
https://leetcode.com/problems/longest-palindromic-substring/

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
## Minimum Domino Rotations For Equal Row
https://leetcode.com/problems/minimum-domino-rotations-for-equal-row/

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

## 97. Interleaving String
https://leetcode.com/problems/interleaving-string/

i 表示到 s1 的 i index, j 表示 s2 的 j index, i+j 就是 s3 的index, 依然考虑为在 2D array 中寻找 S3 path, 继承 i-1 或者 j-1 的状态, 更新即可

转移函数:    

dp[i][0] = s1.charAt(i-1) == s3.charAt(i-1) && dp[i-1][0];

dp[i][j] = dp[i-1][j] && s1.charAt(i-1) == s3.charAt(i+j-1) || dp[i][j-1] && s2.charAt(j-1) == s3.charAt(i+j-1)

```java
 public boolean isInterleave(String s1, String s2, String s3) {
     int n = s1.length(), m = s2.length(), l = s3.length();
     if(n + m != l)  return false;
     // dp[i][j] = dp[i-1][j] && s1.charAt(i-1) == s3.charAt(i+j-1) || dp[i][j-1] && s2.charAt(j-1) == s3.charAt(i+j-1)
     boolean[][] dp = new boolean[n+1][m+1];
     dp[0][0] = true;
     
     for(int i = 1; i <= n; i++){
         dp[i][0] = s1.charAt(i-1) == s3.charAt(i-1) && dp[i-1][0];
     }
     
     for(int i = 1; i <= m; i++){
         dp[0][i] = s2.charAt(i-1) == s3.charAt(i-1) && dp[0][i-1];
     }
     
     for(int i = 1; i <= n; i++){
         for(int j = 1; j <= m; j++){
             dp[i][j] = dp[i-1][j] && s1.charAt(i-1) == s3.charAt(i+j-1) || dp[i][j-1] && s2.charAt(j-1) == s3.charAt(i+j-1);
         }
     }
     return dp[n][m];
 }
```

从表可以看出, DP 只和上一 row 有关, 所以去掉一维, 成为1维DP.    
DP[j]存的实际就是上一行的信息.

```java
 public boolean isInterleave(String s1, String s2, String s3) {
     int n = s1.length(), m = s2.length(), l = s3.length();
     if(n + m != l)  return false;
     
     boolean[] dp = new boolean[m+1];
     for(int i = 0; i <= n; i++){
         for(int j = 0; j <= m; j++){
             if(i == 0 && j == 0){
                 dp[0] = true;
             }else if(i == 0){
                 // 1st row in table, only matching the charAt j and previous state j-1
                 dp[j] = dp[j-1] && s2.charAt(j-1) == s3.charAt(i+j-1);    
             }else if(j == 0){
                 // 1st col in table, dp[j] store the info from last row and only match s1.
                 dp[j] = dp[j] && s1.charAt(i-1) == s3.charAt(i+j-1);
             }else{
                 dp[j] = dp[j] && s1.charAt(i-1) == s3.charAt(i+j-1) || 
                     dp[j-1] && s2.charAt(j-1) == s3.charAt(i+j-1);  
             }
         }
     }
     return dp[m];
 }
```

## 376. Wiggle Subsequence
https://leetcode.com/problems/wiggle-subsequence/

先求导数, 然后设置两种状态: 增加, 减少, 跟据 增加 和 减少 的转移即可

```java
 public int wiggleMaxLength(int[] nums) {
     if(nums.length == 1)    return 1;
     
     int n = nums.length;
     int[] diff = new int[n - 1];
     for(int i = 1; i < n; i++){
         diff[i-1] = nums[i] - nums[i-1];
     }
     // DP
     // dp[i+1][0] = Math.max(dp[i][0], dp[i][1] + 1) if diff[i] > 0
     // dp[i+1][1] = Math.max(dp[i][1], dp[i][0] + 1) if diff[i] < 0
     int[][] dp = new int[n-1][2];
     dp[0][0] = diff[0] > 0 ? 1 : 0;
     dp[0][1] = diff[0] < 0 ? 1 : 0;
     for(int i = 1; i < n-1; i++){
         if(diff[i] > 0){
             dp[i][0] = Math.max(dp[i-1][0], dp[i - 1][1] + 1);
             dp[i][1] = dp[i - 1][1];
         }else if(diff[i] < 0){
             dp[i][1] = Math.max(dp[i-1][1], dp[i - 1][0] + 1);
             dp[i][0] = dp[i - 1][0];
         }else{
             dp[i][0] = dp[i - 1][0];
             dp[i][1] = dp[i - 1][1];
         }
     }
     return Math.max(dp[n-2][0], dp[n-2][1]) + 1;
 }
```
## 2327. Number of People Aware of a Secret
https://leetcode.com/problems/number-of-people-aware-of-a-secret/

top-down DP, 从 Day0 开始, 记录 on each day 知道的人数 dp[i] = sum, sum 范围: [i-forget, i-delay], 在 超过 forget 的时候把 dp[i-forget] 的人数清空即可 

```java
 final static int MOD = 1000000007;
 
 public int peopleAwareOfSecret(int n, int delay, int forget) {
     int[] dp = new int[n+1];
     dp[1] = 1;
     for(int i = 2; i <= n; i++){
         if(i > forget)   dp[i - forget] = 0;
         int sum = 0;
         for(int j = Math.max(0, i - forget); j <= i - delay; j++){
             sum = (sum + dp[j]) % MOD;
         }
         dp[i] = sum;
     }
     int res = 0;
     for(int num : dp){
         res = (res + num) % MOD;
     }
     return res;
 }
```

## 264. Ugly Number II
https://leetcode.com/problems/ugly-number-ii/

记录 2 3 5 三个的对应最小丑数 index 进行 DP 转移即可

```java
 public int nthUglyNumber(int n) {
     int[] ugly = new int[n];
     int index2 = 0, index3 = 0, index5 = 0, min;
     ugly[0] = 1;
     
     for(int i = 1; i < n; i++){
         min = Math.min(ugly[index2] * 2, 
            Math.min(ugly[index3] * 3, ugly[index5] * 5));
         ugly[i] = min;
         if(min == mul2)    index2++;
         if(min == mul3)    index3++;
         if(min == mul5)    index5++;
     }
     return ugly[n-1];
 }
```

# Paint House 系列
## 256. Paint House
https://leetcode.com/problems/paint-house/    

直接 2维DP 即可, i 的最小 cost 是 当前paint cost + 其余两种颜色的最小 paint cost at i-1

```java
 public int minCost(int[][] costs) {
     // min cost at i-th house while painting in color 0-2. 
     // dp[i][3]
     int n = costs.length;
     int[][] dp = new int[n][3];
     
     for(int i = 0; i < 3; i++){
         dp[0][i] = costs[0][i];
     }
     
     // dp[i][0] = cost[i][0] + min(dp[i-1][1], dp[i-1][2]);
     for(int i = 1; i < n; i++){
         for(int j = 0; j < 3; j++){
             dp[i][0] = costs[i][0] + Math.min(dp[i-1][1], dp[i-1][2]);
             dp[i][1] = costs[i][1] + Math.min(dp[i-1][0], dp[i-1][2]);
             dp[i][2] = costs[i][2] + Math.min(dp[i-1][1], dp[i-1][0]);
         }    
     }
     
     return Math.min(dp[n-1][0], Math.min(dp[n-1][1], dp[n-1][2]));
 }
```

## 265. Paint House II
https://leetcode.com/problems/paint-house-ii/



## 1473. Paint House III
https://leetcode.com/problems/paint-house-iii/

三维DP

```java
class Solution {
    int[] hs;
    int[][] cs;
    int m, n, t;
    Integer[][][] dp;
    public int minCost(int[] hs, int[][] cs, int m, int n, int target) {
        this.hs = hs;
        this.cs = cs;
        this.m = m;
        this.n = n;
        t = target;
        dp = new Integer[m + 1][t + 1][n + 2];
        for (int i = 0; i <= n; i++) dp[m][0][i] = 0;
        int res = dfs(0, t, n + 1);  //use a dummy color "n + 1" for a dummy house "-1"
        return res < Integer.MAX_VALUE ? res : -1;
    }
    
    // idx: current house index, rem: remaining target, c: current color
    private int dfs(int idx, int rem, int c) {
        if (rem >= 0 && dp[idx][rem][c] != null) return dp[idx][rem][c];    // memoization
        int res = Integer.MAX_VALUE;
        if (idx >= m || rem < 0) return rem == 0 ? 0 : res; // boundary condition
        if (hs[idx] > 0) {                          // 当前房子 painted
            int diff = (hs[idx] == c ? 0 : 1);      // 颜色不一样, 增加新的 neighbor
            dp[idx][rem][c] = dfs(idx + 1, rem - diff, hs[idx]);
            return dp[idx][rem][c];                 // 储存DP
        }
        for (int i = 1; i <= n; i++) {              // 按顺序上色
            int diff = (i == c ? 0 : 1);            // 判断是否和上一个房子 same color, 增加新的 neighbor
            int last = dfs(idx + 1, rem - diff, i); // 子状态的最小值
            if (last < Integer.MAX_VALUE)           // 如果子状态有效, 则 res = min(res, cost+last)
                res = Math.min(res, cs[idx][i - 1] + last);
        }
        dp[idx][rem][c] = res;                      // 储存DP
        return res;
    }
}
```

## 629. K Inverse Pairs Array
https://leetcode.com/problems/k-inverse-pairs-array/

DP过程隐藏的比较深, 需要仔细去找, DP[n + 1][k + 1] 表示前 n 个数字中, 有 k 个反转对      
当有 [1, n-1] 时, 把 n 放到最后面则是 完全继承了上一时刻的状态 dp[n-1][k];     
把 n 放到倒数第二个位置, 则是继承了 dp[n-1][k-1], 因为此时 n 能提供 1 个反转对;     
把 n 放到第一个位置, 则继承了 dp[n-1][k-(n-1)], 因为此时 n 能提供 n - 1个反转对;

dp[n][k] = dp[n-1][k] + dp[n-1][k-1] + ... + dp[n-1][k-(n-1)]    
dp[n][k+1] = dp[n-1][k+1] + dp[n-1][k] + ... + dp[n-1][k + 1 -(n-1)]    

两式相减得到: dp[n][k+1] = dp[n][k]+dp[n-1][k+1]-dp[n-1][k+1-n]

注意4点: 
1. 起始状态为: 有两个数字 1 和 2, 反转 1 次和 0 次, 结果都是 1
2. edge case: n * (n-1) / 2: 最大的反转对数, 注意不能超过最大反转对数
3. i 从 3 开始, 因为前两个数字 是 base case
4. 注意 MOD 会导致的可能负数, 在减法之后 (x + MOD) % MOD 保证正数

```java
static int MOD = 1000000007;

public int kInversePairs(int n, int k) {
   // k > n*(n-1)/2 表示反转对数 超过上限
   if (k > n*(n-1)/2 || k < 0)                 
      return 0;
   // all pair反过来
   if(k == 0 || k == n * (n - 1) / 2)          
      return 1;

   long[][] dp = new long[n + 1][k + 1];
   dp[2][0] = 1;                               // 两个数字, 无反转 = 1 
   dp[2][1] = 1;                               // 两个数字, 翻转一次 = 1
   for (int i = 3; i <= n; i++) {
      // dp[n][k+1] = dp[n][k]+dp[n-1][k+1]-dp[n-1][k+1-n] where i = n, j = k+1
      dp[i][0] = 1;
      for(int j = 1; j <= Math.min(k, i * (i - 1) / 2); j++){
          dp[i][j] = (dp[i][j-1] + dp[i-1][j]) % MOD;
          if(j >= i)  
              dp[i][j] = (dp[i][j] - dp[i-1][j-i]) % MOD;     // may cause neg
          dp[i][j] = (dp[i][j] + MOD) % MOD;
      }
   }
   return (int) dp[n][k];
}
```


# Max Rectangle 系列
## 221. Maximal Square
https://leetcode.com/problems/maximal-square/

DP, 但是如何确定 正方形? 使用验证技巧 3 MIN:      
DP[i][j] 记录 右下角在 i 和 j 位置的正方形的边长, 如果 DP[i-1][j], DP[i-1][j-1], DP[i][j-1] 均为正方形 (大于 1) , 则      
DP[i][j] = min(DP[i-1][j], DP[i-1][j-1], DP[i][j-1]) + 1     
意味继承最小的有效正方形边长

```java
 public int maximalSquare(char[][] matrix) {
     // dp[i][j] - the side length of the matrix, which right-bot corner at [i, j]
     int n = matrix.length, m = matrix[0].length;
     int dp[][] = new int[n][m], res = 0;
     
     for (int i = 0; i < n; i++) {
         for (int j = 0; j < m; j++) {
             if (matrix[i][j] == '1') {
                 dp[i][j] = Math.max(dp[i][j], 1 + Math.min(dp[Math.max(0, i-1)][j], 
                                         Math.min(dp[Math.max(0, i-1)][Math.max(0, j-1)], 
                                                  dp[i][Math.max(0, j-1)])));
                 res = Math.max(dp[i][j], res);
             }
         }
     }
     return res * res;
 }
```

## 85. Maximal Rectangle
https://leetcode.com/problems/maximal-rectangle/

```java


```


## 238. Product of Array Except Self
https://leetcode.com/problems/product-of-array-except-self/

虽然不是DP, 但是是下面那道题的思路, 使用 prefix 和 suffix 节省计算, 这里简化了 suffix, 采用 right 单独维持

```java
public int[] productExceptSelf(int[] nums) {
    int n = nums.length;
    int[] res = new int[n];
    res[0] = 1;
    for (int i = 1; i < n; i++) {       // calculate prefix
        res[i] = nums[i-1] * res[i-1];
    }
    int right = 1;                      // maintain the suffix
    for (int i = n - 2; i >= 0; i--) {  
        right *= nums[i + 1];
        res[i] *= right;
    }
    return res;
}
```

## 361. Bomb Enemy
https://leetcode.com/problems/bomb-enemy/

带有前后缀思想的DP, 使用DP记录在当前位置能炸死的敌人, 但是需要行和列, 共四个方向的信息, 使用类似prefix 和 suffix的办法, 先更新四个方向的信息, 进行累加, 再 find max

```java
 public int maxKilledEnemies(char[][] grid) {
     // DP[i][j], the enemy can be killed at i row and j col, 
     int n = grid.length, m = grid[0].length, res = 0, cnt = 0;
     int[][] dp = new int[n + 1][m + 1];
     
     for (int i = 0; i < n; i++) {           // calculate row prefix and suffix
         cnt = 0;
         for (int j = 0; j < m; j++) {       // row prefix
             if (grid[i][j] == 'E') cnt++;
             if (grid[i][j] == 'W') cnt = 0;
             if (grid[i][j] == '0') {
                 dp[i][j] += cnt;
             }
         }
         cnt = 0;
         for (int j = m - 1; j >= 0; j--) { // row suffix
             if (grid[i][j] == 'E') cnt++;
             if (grid[i][j] == 'W') cnt = 0;
             if (grid[i][j] == '0') {
                 dp[i][j] += cnt;
             }
         }
     }
     
     for (int j = 0; j < m; j++) {           // calculate col prefix and suffix
         cnt = 0;
         for (int i = 0; i < n; i++) {       // col prefix
             if (grid[i][j] == 'E') cnt++;
             if (grid[i][j] == 'W') cnt = 0;
             if (grid[i][j] == '0') {
                 dp[i][j] += cnt;
                 res = Math.max(res, dp[i][j]);
             }
         }
         cnt = 0;
         for (int i = n - 1; i >= 0; i--) {       // col suffix
             if (grid[i][j] == 'E') cnt++;
             if (grid[i][j] == 'W') cnt = 0;
             if (grid[i][j] == '0') {
                 dp[i][j] += cnt;
                 res = Math.max(res, dp[i][j]);
             }
         }            
     }
     
     return res;
 }
```

## 1162. As Far from Land as Possible
https://leetcode.com/problems/as-far-from-land-as-possible/

**DP + 双向遍历 + overflow处理**, 很简单的最小值 + 1 转移:      
正向遍历, dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i][j]) + 1     
反向遍历, dp[i][j] = min(dp[i+1][j], dp[i][j+1], dp[i][j]) + 1     
但是注意Overflow的情况, 不能使用 Integer.MAX_VALUE: 正向遍历时候, 因为 water cell 一旦不能找到 已计算的node, 则 将被设置为 MAX_VALUE, 下一个node 计算时 + 1 就会造成 Overflow, 所以选择超过 最大 path length 的一个 constant.

```java
 public int maxDistance(int[][] grid) {
     // DP, the min distance from land to curr water cell
     // search Twice, from left-up and right-bottom
     // cause if start from left-up, the right-bottom info is not known
     // setting the water cell initial to 1000 rather than Integer.MAX_VALUE, which cause overflow
     if (grid == null || grid.length == 0)
         return -1;
     
     int res = 0;
     int n = grid.length, m = grid[0].length;
     // search from left-up
     for (int i = 0; i < n; i++) {
         for (int j = 0; j < m; j++) {
             if (grid[i][j] == 1)
                 continue;
             grid[i][j] = 1000;
             if (i - 1 >= 0) grid[i][j] = Math.min(grid[i][j], 1 + grid[i - 1][j]);
             if (j - 1 >= 0) grid[i][j] = Math.min(grid[i][j], 1 + grid[i][j - 1]);
         }
     }
     // search from right-bottom
     for (int i = n - 1; i >= 0; i--) {
         for (int j = m - 1; j >= 0; j--) {
             if (grid[i][j] == 1)
                 continue;
             if (i + 1 < n) grid[i][j] = Math.min(grid[i][j], 1 + grid[i + 1][j]);
             if (j + 1 < m) grid[i][j] = Math.min(grid[i][j], 1 + grid[i][j + 1]);
             res = Math.max(res, grid[i][j]);
         }
     }
     
     return res == 1000 ? -1 : res - 1;
 }
```

## 377. Combination Sum IV
https://leetcode.com/problems/combination-sum-iv/

标准DP, dp[i] += dp[i - num] for num : nums if i - num >= 0

```java
 public int combinationSum4(int[] nums, int target) {
     // DP[i], from 0 to target, indicate the # of combination where the sum is i
     // dp[0] = 1
     // Arrays.sort(nums)
     // dp[i] += dp[i-num] for num: nums, if i - num >= 0
     if (nums == null || nums.length == 0)
         return 0;
     
     Arrays.sort(nums);
     int[] dp = new int[target + 1];
     dp[0] = 1;
     for (int i = 1; i <= target; i++) {
         for (int num : nums) {
             if (i - num < 0)
                 break;
             dp[i] += dp[i - num];
         }
     }
     return dp[target];
 }
```

## 823. Binary Trees With Factors
https://leetcode.com/problems/binary-trees-with-factors/

Map + DP, 最优子状态是: leaf node 已经形成了一个valid product tree, 以次为基础(叶子节点), 上推更多的tree. 使用 map, key 存 root 的值, value 存 # of product tree. 相乘即可. 注意Overflow

```java
final static int MOD = 1000000007;
int res = 0;

public int numFactoredBinaryTrees(int[] arr) {
  // req: node.val = node.left.val * node.right.val
  // basic Idea is: 3Sum
  // map, key is the product, key is # of tree that using key as root
  // iterate the prev part, find all the possible combination 
  // cuz it's unqiue so that map can be used
  
  if (arr == null || arr.length == 0)
      return 0;
  
  Map<Integer, Integer> map = new HashMap<>();
  Arrays.sort(arr);
  for (int num : arr) {
      int cnt = map.getOrDefault(num, 0);
      for (int i = 0; i < arr.length && arr[i] < num; i++) {
          if (num % arr[i] == 0) {                            // can be completely divided
              int rest = num / arr[i];        
              if (map.containsKey(rest)) {                    // deal with Overflow using long
                  long cnt1 = (long) map.get(rest) % MOD, cnt2 = (long) map.get(arr[i]) % MOD;
                  long p = cnt1 * cnt2 % MOD;
                  cnt = (cnt + (int) p) % MOD;
              }
          }
      }
      map.put(num, (cnt + 1) % MOD);
  }
  
  for (Map.Entry<Integer, Integer> e : map.entrySet()) {
      res = (res % MOD + e.getValue() % MOD) % MOD;
  }
  
  return res % MOD; 
}
```

## 96. Unique Binary Search Trees
https://leetcode.com/problems/unique-binary-search-trees/

变形了的 树形DP, dp[i] 表示 # of unique BST. 对于有 i 个节点的 tree, 其 root 节点也就有 i 个, 将不同的节点作为 root, 其左右子树的大小就是: j - 1 和 i - j, 这些大小的树是之前计算过的子状态, 相乘累加即可.

```java
 public int numTrees(int n) {
     int[] dp = new int[n + 1];
     dp[0] = dp[1] = 1;
     
     for (int i = 2; i <= n; i++) {
         for (int j = 1; j <= i; j++) {
             dp[i] += dp[j-1] * dp[i-j];
         }
     }
     return dp[n];
 }
```

## 1770. Maximum Score from Performing Multiplication Operations
https://leetcode.com/problems/maximum-score-from-performing-multiplication-operations/

自底向上, 记忆化DFS, 使用 memo 记录 nums左边界为 p1, multi index 为 p2 时的最大值. 注意求右边的位置即可. 

```java
class Solution {
    int[][] memo = new int[1001][1001];

    public int maximumScore(int[] nums, int[] multipliers) {
        // bottom-up memo DFS
        return dfs(nums, multipliers, 0, 0);
    }
    
    private int dfs(int[] nums, int[] m, int p1, int p2) {
        if (p2 >= m.length) 
            return 0;
        
        if (memo[p1][p2] != 0)
            return memo[p1][p2];
        
        int p1End = nums.length - 1 - (p2 - p1);
        memo[p1][p2] = Math.max(nums[p1] * m[p2] + dfs(nums, m, p1 + 1, p2 + 1),
                                    nums[p1End] * m[p2] + dfs(nums, m, p1, p2 + 1));

        return memo[p1][p2];
    }
}
```

## 718. Maximum Length of Repeated Subarray
https://leetcode.com/problems/maximum-length-of-repeated-subarray/

求最长公共子数组, DP. dp[i][j] 表示 nums1 的 0-i 区间 和 nums2 的 0-j 区间 的 公共子区间   
dp[i][j] = dp[i-1][j-1] + 1 if nums1[i-1] == nums2[j-1].

```java
 public int findLength(int[] nums1, int[] nums2) {
     if (nums1 == null || nums1.length == 0 || nums2 == null || nums2.length == 0)
         return 0;
     
     int n = nums1.length, m = nums2.length, res = 0;
     int[][] dp = new int[n + 1][m + 1];
     for (int i = 1; i <= n; i++) {
         for (int j = 1; j <= m; j++) {
             if (nums1[i - 1] == nums2[j - 1]) {
                 dp[i][j] = dp[i - 1][j - 1] + 1;
                 res = Math.max(res, dp[i][j]);
             } else 
                 dp[i][j] = 0;
         }
     }
     return res;
 }
```

## 2272. Substring With Largest Variance
https://leetcode.com/problems/substring-with-largest-variance/

pair 对应 + 高级版本的Kadane 算法. 

什么是Kadane, O(n)一次扫描数组, 并记录以扫描点结尾的子数组信息. 使用最优子结构, 则以 i + 1 结尾的子数组应该基于 以 i 结尾的子数组.

两次遍历all char, 获取所有 char pair. 针对每个pair, 一个 char freq a 为增长, 另一个 char freq b 为减少, 记录到 i 为止得到子数组信息.

如果 c == a, 则增长++; 如果 c == b, 则减少--, 并同时减少其上限, 作为判断是否还存在后续的标准; 

```java
class Solution {
    public int largestVariance(String s) {
        // dp 从 i 到 j 的最大
        if (s == null || s.length() == 0)
            return 0;
        
        int[] freq = new int[26];
        for (char c : s.toCharArray()) {
            freq[c - 'a']++;
        }
        
        int n = s.length(), res = 0;
        for (int i = 0; i < 26; i++) {
            for (int j = 0; j < 26; j++) {
                int aMaxFreq = freq[i], bMaxFreq = freq[j];
                if (i == j || aMaxFreq == 0 || bMaxFreq == 0)   
                    continue;
                
                int aCurFreq = 0, bCurFreq = 0;
                for (char c : s.toCharArray()) {
                    if (c - 'a' == j)
                        bCurFreq++;
                    
                    if (c - 'a' == i) {
                        aCurFreq++;
                        aMaxFreq--;
                    }
                    
                    if (aCurFreq > 0)   // a 存在子数组中
                        res = Math.max(res, bCurFreq - aCurFreq);
                    
                    if (aCurFreq > bCurFreq && aMaxFreq >= 1) {
                        aCurFreq = bCurFreq = 0;
                    }
                }   
            }
        }
        return res;
    }
}
```

## 87. Scramble String
https://leetcode.com/problems/scramble-string/

top-down DP. 从 s1 开始, 遍历所有分开的点, 左侧为: s1.substring(0, i) 右侧为: s1.substring(i), 在每个点都有两个选择, 换和不换. 不换则 s2 也用相同办法分开, 换则 s2.substring(n-i) 和 s2.substring(0, n-i). 并使用 序列化 存进 map 实现 memo. 

```java
class Solution {
    Map<String, Boolean> map = new HashMap<>();
    
    public boolean isScramble(String s1, String s2) {
        if (s1.equals(s2) || s1.isEmpty())
            return true;
        return dfs(s1, s2);
    }
    
    private boolean dfs(String s1, String s2) {
        if (s1.equals(s2))
            return true;
        
        if (s1.length() <= 1)
            return false;
        
        int n = s1.length();
        String memo = s1 + "#" + s2;
        if (map.containsKey(memo))
            return map.get(memo);
        
        boolean res = false;
        for (int i = 1; i < n; i++) {
            res |= dfs(s1.substring(0, i), s2.substring(0, i)) && dfs(s1.substring(i), s2.substring(i));
            res |= dfs(s1.substring(0, i), s2.substring(n - i)) && dfs(s1.substring(i), s2.substring(0, n - i));
            if (res)
                break;
        }
        map.put(memo, res);
        return res;
    }
}
```

## 1155. Number of Dice Rolls With Target Sum
https://leetcode.com/problems/number-of-dice-rolls-with-target-sum/

记忆化 + DFS = DP. dp[i][j] 表示 第 i 个骰子, 总和为 j. bottom-up 更新. 当且仅当 总和 为 target, 设为 1.

```java
 final static int MOD = 1000000007;
 public int numRollsToTarget(int n, int k, int target) {
     // n dice, k faces from [1, k]
     // dp[i][j] - the combinations at i dice, j is the sum
     int[][] dp = new int[n + 1][target + 1];
     for (int[] arr : dp)
         Arrays.fill(arr, -1);
     
     dfs(dp, 0, n, k, target, 0);
     return dp[0][0];
 }
 
 private int dfs(int[][] dp, int start, int n, int k, int target, int sum) {
     if (start == n)
         return sum == target ? 1 : 0;
     
     if (dp[start][sum] != -1)
         return dp[start][sum];
     
     int res = 0;
     for (int i = 1; i <= Math.min(k, target - sum); i++) {
         res = (res + dfs(dp, start + 1, n, k, target, sum + i)) % MOD;
     }
     dp[start][sum] = res;
     return res;
 }
```

## 2435. Paths in Matrix Whose Sum Is Divisible by K
https://leetcode.com/problems/paths-in-matrix-whose-sum-is-divisible-by-k/

考虑 取模 的分配律: (a + b) % k = (a % k + b % k) % k.    
使用三维DP, **多考虑一个在当前位置, 余数为 mod 时候的路径数量**即可.

```java
class Solution {
    final static int MOD = 1000000007;
    int k = 0;
    Integer[][][] dp; 
    
    
    public int numberOfPaths(int[][] grid, int k) {
        int m = grid.length, n = grid[0].length;
        this.k = k;
        dp = new Integer[m][n][k];
        return dfs(0, 0, 0, m, n, grid);
    }
    
    private int dfs(int row, int col, int mod, int m, int n, int[][] grid) {
        if (row >= m || col >= n)
            return 0;
        
        if (row == m - 1 && col == n - 1) {
            if ((mod + grid[row][col]) % k == 0)
                return 1;
            
            return 0;
        }
        
        if (dp[row][col][mod] != null)
            return dp[row][col][mod];
        
        dp[row][col][mod] = 0;
        dp[row][col][mod] = (dp[row][col][mod] + dfs(row + 1, col, (mod + grid[row][col]) % k, m, n, grid)) % MOD;
        dp[row][col][mod] = (dp[row][col][mod] + dfs(row, col + 1, (mod + grid[row][col]) % k, m, n, grid)) % MOD;
        
        return dp[row][col][mod];
    }
}
```
## 1531. String Compression II
https://leetcode.com/problems/string-compression-ii/

思考路线: 为什么DP? 因为 1. 存在删除状态和未删除状态. 2. 存在 从 0 到 i 的信息       
几维DP? 2维, 因为删除操作始终发生在 0 到 i, 还有个删了多少次的操作      
转移函数: 直接删除则 dp[i][j] = dp[i-1][j-1]         
复杂情况的转移: 从当前位置往回找, 把所有不同的字符删了, 然后沿途更新       

```java
public int getLengthOfOptimalCompression(String s, int k) {
  // dp[i][k]  到 i 为止, 删 k 个时候的最短
  // 如果直接删当前的, dp[i][k] = dp[i-1][k-1]
  // 从当前 char 往前找到 index-0, 把中间不同的删了, 看看能组成的长度, 并更新相应位置的状态
  int n = s.length();
  int[][] dp = new int[n + 1][k + 1];
  for (int i = 0; i <= n; i++) {
      for (int j = 0; j <= k; j++) {
          dp[i][j] = 300;
      }
  }
  
  dp[0][0] = 0;
  for (int i = 1; i <= n; i++) {
      for (int j = 0; j <= k; j++) {
          // 从当前为止找到0, 看下删掉的中间不同的, 到每个为止时候
          int cnt = 0, del = 0;
          for (int l = i; l >= 1; l--) {
              if (s.charAt(l - 1) == s.charAt(i - 1))
                  cnt++;
              else 
                  del++;
              if (j - del >= 0)   // 删除次数小于当前状态删除次数
                  dp[i][j] = Math.min(dp[i][j], 
                                      dp[l-1][j-del] + 1 + getNumLen(cnt));
          }
          if (j > 0)  // 直接删除, 直接继承
              dp[i][j] = Math.min(dp[i][j], dp[i-1][j-1]);
      }
  }
  
  return dp[n][k];
}

private int getNumLen(int cnt) {
  return cnt >= 100 ? 3 : cnt >= 10 ? 2 : cnt >= 2 ? 1 : 0;
}
```

## 1335. Minimum Difficulty of a Job Schedule
https://leetcode.com/problems/minimum-difficulty-of-a-job-schedule/

```java
int n;
public int minDifficulty(int[] jobDifficulty, int d) {
  // 存在依赖关系, 一天至少一个活
  // 每天的diffculty是当天最难的那个
  // 把任务 切 d-1 刀, 让每个部分的最大值 的 和 最小
  // 两个状态: 剩余刀数 和 砍的位置
  this.n = jobDifficulty.length;
  if (d > n)
      return -1;
  
  int[][] dp = new int[n + 1][d + 1];
  for (int[] a : dp)
      Arrays.fill(a, -1);
  
  return dfs(d, 0, jobDifficulty, dp);
}

private int dfs(int remainCut, int startIndex, int[] jobDifficulty, int[][] dp) {
  if (remainCut == 0 && startIndex == n)  // 抵达终点
      return 0;
  if (remainCut == 0 || startIndex == n)  // 越界搜索
      return Integer.MAX_VALUE;
  if (dp[startIndex][remainCut] != -1)
      return dp[startIndex][remainCut];
  
  int max = jobDifficulty[startIndex];     // 记录最大难度
  int res = Integer.MAX_VALUE;
  
  for (int i = startIndex; i < n; i++) {
      max = Math.max(max, jobDifficulty[i]);     // 更新最大难度
      int t = dfs(remainCut - 1, i + 1, jobDifficulty, dp);   // 在 i 处砍下 1 刀
      if (t != Integer.MAX_VALUE)                 // 砍的有效, 更新当前位置的最小值
          res = Math.min(res, t + max);
  }
  return dp[startIndex][remainCut] = res;
}
```