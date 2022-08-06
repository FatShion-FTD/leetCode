# Greedy

贪心算法, 常见技术: 统计最后出现位置, 多指针, 二分, 统计出现次数


## Partition Labels
https://leetcode.com/problems/partition-labels/

1. 遍历统计最后出现位置
2. 记录当前子串中每个char的最后出现位置currEnd, 和index比较,相同则减去上一字串的末尾lastEnd, 加入结果

```java
public List<Integer> partitionLabels(String s) {
    int[] lasts = new int[26];
    for(int i = 0; i < s.length(); i++){
        lasts[s.charAt(i) - 'a'] = i;
    }
    
    List<Integer> res = new ArrayList<>();
    int currEnd = 0, lastEnd = 0;
    for(int i = 0; i < s.length(); i++){
        currEnd = Math.max(currEnd, lasts[s.charAt(i) - 'a']);
        if(currEnd == i){
            res.add(currEnd - lastEnd + 1);
            lastEnd = i + 1;
        }
    }
    
    return res;
}
```

## Smallest String With A Given Numeric Value
https://leetcode.com/problems/smallest-string-with-a-given-numeric-value/

贪心点: 只有n个z, m个a 还有 一个 a-z 的char组成, 先计算z的数量, 再计算剩下的.

```java
 public String getSmallestString(int n, int k) {
     char[] chars = new char[n];
     for(int i = 0; i < n; i++){
         chars[i] = 'a';
     }
     k -= n;
     while(k != 0){
         if(k>=25){
             chars[n-1] = 'z';
             n--;
             k-=25;
         }else{
             chars[n-1] = (char)('a' + k);
             k = 0;
             n--;
         }
     }
     return String.valueOf(chars);
 }
```

## Broken Calculator
https://leetcode.com/problems/broken-calculator/

反向Greedy代表, 从结果往开始条件推, 确保target下降最快. 最后计算 初始值需要减少的量即可

```java
 public int brokenCalc(int s, int t) {
     int count = 0;
     while(t > s){
         if(t % 2 == 0){
             t /= 2;
         }else{
             t += 1;
         }
         count++;
     }
     return count + (s - t);
 }
```

## 2 Keys Keyboard
https://leetcode.com/problems/2-keys-keyboard/

使用遍历每个长度的办法, 首先dp[i] = i 意为 直接从一个A复制过来所需要的次数; 反向遍历 j 直到找到最大的乘数, new dp[i] = 达成 dp[j] 的次数 + 倍数 

```java
 public int minSteps(int n) {
     int[] dp = new int[n+1];
     for(int i = 2; i <= n; i++){
         dp[i] = i;  // 直接从A复制
         for(int j = i-1; j >= 2; j--){
             if(i % j == 0){ // j 是 i 的最大乘数
                 dp[i] = dp[j] + (i/j);
                 break;
             }
         }
     }
     return dp[n];
 }
```

## Boats to Save People
https://leetcode.com/problems/boats-to-save-people/

注意一个点: 一个船只能坐俩人, 双指针更容易

```java
 public int numRescueBoats(int[] p, int l) {
     Arrays.sort(p);
     int count = 0, left = 0, right = p.length-1, temp = 0;
     while(right >= left){
         if(p[left] + p[right] <= l){
             right--;
             left++;
         }else{
             right--;
         }
         count++;
     }
     return count;
 }
```

## Two City Scheduling
https://leetcode.com/problems/two-city-scheduling/

核心: 排序 + lambda表达式. 计算去A赚钱 a[0] - a[1] 的结果, 把去A赚最多的和去B亏最少的 (等效) 排序. 按顺序分配即可. (a, b) -> (a[0] - a[1]) - (b[0] - b[1]) 中 a - b 相当于升序

```java
 public int twoCitySchedCost(int[][] costs) {
     int total = 0;
     int n = costs.length / 2;
     Arrays.sort(costs, (a, b) -> (a[0] - a[1]) - (b[0] - b[1]));
     for(int i = 0; i < n; i++){
         total += costs[i][0] + costs[i+n][1];
     }
     return total;
 }
```

## 277. Find the Celebrity
https://leetcode.com/problems/find-the-celebrity/

使用栈实现带有排除的两两比较, stack储存不认识别的人, 每次取出栈顶的两个元素, 看看他俩是否互相认识, 非 celebrity 必须认识 celebrity, 则每次都能去掉一个

```java
 public int findCelebrity(int n) {
     Deque<Integer> stack = new ArrayDeque<>();
     for(int i = 0; i < n; i++)  stack.push(i);
     while(stack.size()>1){
         int a = stack.pop();
         int b = stack.pop();
         if(knows(a,b)){
             stack.push(b);
         }else{
             stack.push(a);
         }
     }
     int candidate = stack.pop();
     for(int i = 0; i < n; i++){
         // 当i不为candidate时, candidate有不认识的人 或 有人不认识candidate则退出
         if(i!= candidate && (knows(candidate, i) || !knows(i, candidate)))   return -1;
     }
     return candidate;
 }
```


## 2311. Longest Binary Subsequence Less Than or Equal to K
https://leetcode.com/problems/longest-binary-subsequence-less-than-or-equal-to-k/

从右往左尽量找0, 同时 maintain 最高位为 1 时的情况, 用于 从 k 中减去

```java
public int longestSubsequence(String s, int k) {
    int res = 0, tot = 1, n = s.length();
    for(int i = n-1; i >= 0; i--){
        if(tot <= k || s.charAt(i) == '0'){ // 最大值允许 或 当前为 0
            k -= tot * (s.charAt(i) - '0');
            res++;
        }
        if(tot <= k){
            tot *= 2;   // 左移一位, 保持最高位为 1 的最大值
        }
    }
    return res;
}
```
## 665. Non-decreasing Array
https://leetcode.com/problems/non-decreasing-array/

两种情况: 统计操作次数即可
1. [1 3 2 4], 此时 n[i] = 2, n[i-2] = 1 <= n[i], 把 3 拉下去, 变成2; 
2. [1 4 5 2], 此时 n[i] = 2, n[i-2] = 4 > n[i], 把 2 拉上来, 变成5

```java
public boolean checkPossibility(int[] nums) {
   int count = 0;
   for(int i = 1; i < nums.length; i++){
       if(nums[i] < nums[i-1]){
           if(i == 1 || nums[i-2] <= nums[i]){
               nums[i-1] = nums[i];
           }else{
               nums[i] = nums[i-1];
           }
           count++;
       }
   }
   return count <= 1;
}
```

## 1647. Minimum Deletions to Make Character Frequencies Unique
https://leetcode.com/problems/minimum-deletions-to-make-character-frequencies-unique/

统计 freq + HashSet 记录出现的次数 + 如果出现过, 则减去 1 直到没出现过

```java
 public int minDeletions(String s) {
     int[] freq = new int[26];
     for(char c : s.toCharArray())   freq[c-'a']++;
     Set<Integer> used = new HashSet<>();
     int res = 0;
     for(int i = 0; i < 26; i++){
         int f = freq[i];
         while(f > 0){
             if(!used.contains(f)){
                 used.add(f);
                 break;
             }
             f--;
             res++;
         }
     }
     return res;
 }
```

## 406. Queue Reconstruction by Height
https://leetcode.com/problems/queue-reconstruction-by-height/

概念: 找到没有冲突的一组, 则其相对位置是固定. 实现: 
1. 找到身高最高的一组, 他们和后续的没有冲突, 把他们的相对位置固定后, 再用身高较低的进行同样的循环.
2. 首先对身高的 和 前面的人排序, 如果身高不同, 身高降序; 否则按照 前面的人 升序 排序

```java
 public int[][] reconstructQueue(int[][] people) {
     // pq sorted by the HEIGHT desecnding order; FRONT ascending order
     Arrays.sort(people, (o1, o2) -> o1[0] - o2[0] == 0 ? o1[1] - o2[1]: o2[0] - o1[0]);
     // 7,0  7,1  6,1  5,0  5,2  4,4
     LinkedList<int[]> res = new LinkedList<>();
     for(int[] p : people)   res.add(p[1], p);
     return res.toArray(new int[people.length][2]);
 }
```
## 134. Gas Station
https://leetcode.com/problems/gas-station/

计算差值的同时, 维护一个 sum, 作为 preffix Sum, 验证到 i 为止, 是否可行; 不可行, 则 start = i + 1, sum = 0, 重新计数

```java
 public int canCompleteCircuit(int[] gas, int[] cost) {
     int n = gas.length;
     int sum = 0, tot = 0, start = 0;;
     for(int i = 0; i < n; i++){
         int dif = gas[i] - cost[i];
         tot += dif;
         sum += dif;
         if(sum < 0){
             start = i + 1;
             sum = 0;
         }
     }
     return tot < 0 ? -1 : start;
 }
```

## 2332. The Latest Time to Catch a Bus
https://leetcode.com/problems/the-latest-time-to-catch-a-bus/

要上车时间最晚, 就贪心两种情况, 在一个人前面上车 或者 卡点上车. 用 set 存无效的上车时间, 然后在和每个人抢座, 最后如果还有位置, 则卡点上车

```java
 public int latestTimeCatchTheBus(int[] buses, int[] passengers, int capacity) {
     // 2 situation: if not max, arrive at current bus departure time
     // if max, get to the latest time point, passengers[p] - 1
     Set<Integer> set = new HashSet<>();
     int res = 0;
     for (int p : passengers) 
         set.add(p);                                 // use for checking passengers[p] - 1 is valid
     
     Arrays.sort(buses);
     Arrays.sort(passengers);
     for (int i = 0, j = 0; i < buses.length; i++) { // i - bus index, j - passenager index
         int cnt = 0;
         while(cnt < capacity && j < passengers.length && passengers[j] <= buses[i]) {
             if (!set.contains(passengers[j] - 1))
                 res = Math.max(res, passengers[j] - 1);
             cnt++;
             j++;
         }
         if(cnt < capacity && !set.contains(buses[i]))
             res = Math.max(res, buses[i]);
     }
     
     return res;
 }
```

## 435. Non-overlapping Intervals
https://leetcode.com/problems/non-overlapping-intervals/

去掉最少的 intervals 来使得没有重叠 -> 找最多的 non-overlapping intervals   
为了找更多的 intervals, 需要使得 剩余空间越大越好(更多的空间意味着更多的可能), 而不是开始时间越早越好.   
如: [1, 10], [2, 3], [4, 5] 如果选择开始时间早, 则需要去掉两个; 选择剩余时间最多, 则只用去掉一个.    

```java
 public int eraseOverlapIntervals(int[][] intervals) {
     // sort by the end time in ascend order, compare the prev end and curr start, if overlap, res ++; else, prev end = curr end.
     if (intervals == null || intervals.length <= 1)
         return 0;
     
     Arrays.sort(intervals, (o1, o2) -> o1[1] - o2[1]);
     int end = intervals[0][1], res = 0;
     for (int i = 1; i < intervals.length; i++) {
         if (intervals[i][0] >= end) {
             end = intervals[i][1];
         } else {
             res++;
         }
     }        
     return res;
 }
```


## 452. Minimum Number of Arrows to Burst Balloons
https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/

和上一题类似的 interval 问题, 这次需要把有 overlap 的 merge 起来, 并找到所有的 distinct interval. 注意 end 每次 merge 时候需要找 min

```java
 public int findMinArrowShots(int[][] points) {
     // find # of the distinct intervals
     if (points == null || points.length == 0)
         return 0;
     
     Arrays.sort(points, (o1, o2) -> Integer.compare(o1[0], o2[0]));
     int end = points[0][1], res = 1;
     
     for (int i = 1; i < points.length; i++) {
         if (points[i][0] <= end) {
             end = Math.min(end, points[i][1]);
         } else {
             res += 1;
             end = points[i][1];
         }
     }
     
     return res;
 }
```