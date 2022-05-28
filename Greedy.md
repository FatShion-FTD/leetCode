# Greedy

贪心算法, 常见技术: 统计最后出现位置, 多指针, 二分, 统计出现次数


Partition Labels: https://leetcode.com/problems/partition-labels/

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

Smallest String With A Given Numeric Value: https://leetcode.com/problems/smallest-string-with-a-given-numeric-value/

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

Broken Calculator: https://leetcode.com/problems/broken-calculator/

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

2 Keys Keyboard: https://leetcode.com/problems/2-keys-keyboard/

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

Boats to Save People: https://leetcode.com/problems/boats-to-save-people/

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

Two City Scheduling: https://leetcode.com/problems/two-city-scheduling/

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

277. Find the Celebrity: https://leetcode.com/problems/find-the-celebrity/

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