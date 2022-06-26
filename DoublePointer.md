# 双指针

双指针和二分很像

Next Permutation: https://leetcode.com/problems/next-permutation/

如何确定重新排列组合后的值最小且大于原值? 从右往左数,序列为单增, 如 3 2 1, 反转后即可得到1 2 3 为最小, 找到单增序列停止的 k ;

再次从右往左找第一个大于nums[k] 的值的 l , k 和 l 二者交换位置即可得到 更小的右侧序列 + 大于原值 的结果; 

反转 k 的右侧即可

```java
public void nextPermutation(int[] nums) {
    int n = nums.length, k = 0, l = 0;
    for(k = n - 2; k >= 0; k--){    // 查找第一个从右往左的非单增index
        if(nums[k+1] > nums[k]) break;
    }
    if(k<0){
        int last = n - 1, prev = 0;
        while(last > prev){
            int temp = nums[prev];
            nums[prev++] = nums[last];
            nums[last--] = temp;
        }
    }else{
        for(l = n-1; l >= 0; l--){  // 从右往左找第一个比nums[k]大的
            if(nums[l] > nums[k]){
                int temp = nums[l];
                nums[l] = nums[k];
                nums[k] = temp;
                break;
            }
        }
        int last = n-1, prev = k+1;
        while(last > prev){
            int temp = nums[prev];
            nums[prev++] = nums[last];
            nums[last--] = temp;
        }
    }
    return;
}
```

Container With Most Water: https://leetcode.com/problems/container-with-most-water/

经典的双指针问题, 同时用 Greedy 办法解决: 前缀最大, 后缀最大 + 双指针移动; 前缀和后缀 计算到 当前位置的最大高度(从左到右 和 从右到左); 双指针, 哪个小了, 移动哪个

```java
public int maxArea(int[] height) {
    int n = height.length;
    int[] pre = new int[n];
    int[] suf = new int[n];
    int max = Integer.MIN_VALUE;
    
    for(int i = 0; i < n; i++){
        pre[i] = Math.max(max, height[i]);
        max = pre[i];
    }
    
    max = Integer.MIN_VALUE;
    for(int i = n- 1; i >= 0; i--){
        suf[i] = Math.max(max, height[i]);
        max = suf[i];
    }
    
    int left = 0, right = n - 1, res = 0;
    while(right > left){
        res = Math.max(res, Math.min(pre[left], suf[right]) * (right - left));
        if(pre[left] > suf[right]){
            right--;
        }else{
            left++;
        }
    }
    return res;
}
```

3Sum: https://leetcode.com/problems/3sum/

经典 3Sum, 三点: 将三指针 简化为 双指针 + sort和while去重 + Arrays.asList(num1, num2) 新建

```java
public List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    int tot = 0;
    int n = nums.length;
    List<List<Integer>> res = new ArrayList<>();
    
    for(int i = 0; i < n-2; i++){
        if(nums[i] > 0) break;
        if(i == 0 || i > 0 && nums[i] != nums[i-1]){
            int low = i + 1, high = n-1;            
            while(high > low){
                if(nums[high] + nums[low] == -nums[i]){
                    res.add(new ArrayList<>(Arrays.asList(nums[i], nums[high], nums[low])));
                    while(high > low && nums[low] == nums[low+1]) low++;
                    while(high > low && nums[high] == nums[high-1]) high--;
                    low++;
                    high--;
                }else if(nums[high] + nums[low] > -nums[i]){
                    high--;
                }else{
                    low++;
                }
            }
        }
    }
    return res;
}
```

3Sum With Multiplicity: https://leetcode.com/problems/3sum-with-multiplicity/

3Sum 变形. 两点: 3Sum原型 + 使用乘法计算重复出现的次数

在使用乘法时候, 注意一点: 如果 arr[low] == arr[high], sameHigh == sameLow, 表现为 (X, Y, Y), 则不能相乘, 需要转换为求和公式 n * (n-1) / 2

例子: 4,4,4,4, {(0,1), (1,2), (2,3)} = 3, {(1,2), (2,3)} = 2, {(2,3)} = 1, 使用n = 4的求和公式 1 + 2 + 3

```java
public int threeSumMulti(int[] arr, int target) {
    int res = 0;
    Arrays.sort(arr);
    int n = arr.length;
    
    for(int i = 0; i < n - 2; i++){
        int temp = target - arr[i];
        int low = i + 1, high = n - 1;
        while(high > low){
            if(arr[high] + arr[low] == temp){
                int sameHigh = 1, sameLow = 1;
                while(high > low && arr[low] == arr[low+1]){
                    low++;
                    sameLow++;
                } 
                while(high > low && arr[high] == arr[high-1]){
                    high--;
                    sameHigh++;
                } 
                if(low == high){
                    res = (res + sameLow * (sameLow - 1) / 2) % 1000000007;
                }else res = (res + sameHigh * sameLow) % 1000000007;
                low++;
                high--;
            }else if(arr[high] + arr[low] > temp){
                high--;
            }else{
                low++;
            }
        }
    }
    
    return res % 1000000007;
}
```

优化版本: 使用 MAP 记录 MAP<arr[j] + arr[k], #>, 遍历 arr[i], 获得 target - arr[i] 即可 

```java
public int threeSumMulti(int[] arr, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    int res = 0, mod = 1000000007;
    for(int i = 0; i < arr.length; i++){
        res = (res + map.getOrDefault(target - arr[i], 0)) % mod;
        for(int j = 0; j < i; j++){
            int temp = arr[i] + arr[j];
            map.put(temp, map.getOrDefault(temp, 0) + 1);
        }
    }
    return res;
}
```


# Sliding Window 滑窗

**Uber 面试题** Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit: https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/

两个要点: 使用 双端队列(Deque) 存储 最大值 和 最小值 们 + 滑窗思想右滑

使用 Deque 的结果: 在 maxQue 当中按顺序存储所有 大于num[right] 的值; 在 minque 当中按顺序存储所有 小于num[right]的值

使用 Deque 存 最大值最小值队列: 保持最大值队列 (MAX HEAP) 的 最后一个元素比 nums[right] 大; 最小值队列 (MIN HEAP) 的 最后一个元素比 nums[right] 小;

如果 不满足 < limit 的条件, 则判断是否是 head of deque, 如果是则 pollFirst(), 将 left ptr 右划, 直到满足条件
```java
public int longestSubarray(int[] nums, int limit) {
    int n = nums.length;
    int left = 0, right = 0, res = 0;
    Deque<Integer> maxQueue = new ArrayDeque<>();
    Deque<Integer> minQueue = new ArrayDeque<>();
    
    while(right < n){
        while(!maxQueue.isEmpty() && nums[right] > maxQueue.peekLast()){
            maxQueue.removeLast();
        }
        maxQueue.add(nums[right]);
        while(!minQueue.isEmpty() && nums[right] < minQueue.peekLast()){
            minQueue.removeLast();
        }
        minQueue.add(nums[right]);
            
        while(maxQueue.peekFirst() - minQueue.peekFirst() > limit){
            if(maxQueue.peekFirst() == nums[left])  maxQueue.pollFirst();
            if(minQueue.peekFirst() == nums[left])  minQueue.pollFirst();
            left++;
        }
        right++;
        res = Math.max(res, right - left);
    }
    return res;
}
```

## SubArray & Subsequence

### Longest common subarray, 滑窗即可, 也可DP
```java
public int longestCommonSubarray(String s1, String s2) {
    int len = 0;
    int[] subW1 = new int[w1.length()];
    for(int i = 0; i < w1.length(); i++){
        int count = 0, k = i, j = 0;
        while(j < w2.length()){
            while(j < w2.length() && k < w1.length() && w1.charAt(k) == w2.charAt(j)){
                count += 1;
                k++;j++;
            }
            subW1[i] = Math.max(subW1[i], count);
            count = 0;
            k = i;
            j++;
        }
    }
    for(int i = 0; i < w1.length(); i++)    len = Math.max(len, subW1[i]);
    return len;
}
// Subarray 体现为 没有任何非连续状态继承
public int longestCommonSubarray(String s1, String s2) {
    int n = s1.length(), m = s2.length();
    int[][] dp = new int[n+1][m+1];
    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++){
            if(s1.charAt(i-1) == s2.charAt(j-1)){
                dp[i][j] = dp[i-1][j-1] + 1;
            }else{
                dp[i][j] = 0;
            }
        }
    }
    return dp[n][m];
}
```

### 1143. Longest Common Subsequence (LCS)
https://leetcode.com/problems/longest-common-subsequence/

DP[i][j] 记录 到 i 和 j 为止的最长子串长度, 如果 i-1 和 j-1 相同则: 

dp[i][j] = dp[i-1][j-1] + 1;

否则, 选择继承 i-1 和 j-1 的最大的状态, 而不是再重新遍历:

dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);

```java
public int longestCommonSubsequence(String s1, String s2) {
    int n = s1.length(), m = s2.length();
    int[][] dp = new int[n+1][m+1];
    for(int i = 1; i <= n; i++){
        for(int j = 1; j <= m; j++){
            if(s1.charAt(i-1) == s2.charAt(j-1)){
                dp[i][j] = dp[i-1][j-1] + 1;
            }else{
                dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
            }
        }
    }
    return dp[n][m];
}
```

## 189. Rotate Array
https://leetcode.com/problems/rotate-array/

Rotate 的核心在于 reverse, 不管是 rotate array 还是 rotate matrix, 都可以用 **reverse**. 

对于 Array, 将 array 分为两个部分, 分别reverse, 再reverse即可的都结果. 

1 2 3 4 5 6 -> 4 3 2 1 5 6 -> 4 3 2 1 6 5 -> 5 6 1 2 3 4

对于 rotate matrix, 则是使用 transpose(也是一种 reverse), transpose 后再 reverse

```java
public void rotate(int[] nums, int k) {
    int shift = k % nums.length, n = nums.length;
    int start = 0, newStart = n - shift;
    reverse(nums, 0, n - shift - 1);
    reverse(nums, n - shift, n - 1);
    reverse(nums, 0, n - 1);        
    return;
}

private void reverse(int[] nums, int start, int end){
    while(start < end){
        int temp = nums[start];
        nums[start++] = nums[end];
        nums[end--] = temp;
    }
    return;
}
```

## 1229. Meeting Scheduler
https://leetcode.com/problems/meeting-scheduler/

先排序, 再用 max(start1, start2) 和 min(end1, end2) 找到区间, 判断区间大小, 跟据 end 大小判断 i++ or j++. 可以免去 区间intersection 的判断

```java
public List<Integer> minAvailableDuration(int[][] slots1, int[][] slots2, int duration) {
    Arrays.sort(slots1, (o1, o2) -> o1[1] - o2[1]);
    Arrays.sort(slots2, (o1, o2) -> o1[1] - o2[1]);
    int n1 = slots1.length, n2 = slots2.length, i = 0, j = 0;
    List<Integer> res = new ArrayList<>();
    while(i < n1 && j < n2){
        int start = Math.max(slots1[i][0], slots2[j][0]);
        int end = Math.min(slots1[i][1], slots2[j][1]);
        if(end - start >= duration){
            res.add(start);
            res.add(start+ duration);
            return res;
        }
        if(slots1[i][1] < slots2[j][1])  i++;
        else j++;
    }
    return res;
}
```