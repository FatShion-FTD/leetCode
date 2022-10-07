# PreSum 系列
## 560. Subarray Sum Equals K
https://leetcode.com/problems/subarray-sum-equals-k/

使用 HashMap 记录 preSum 出现的次数, 思路类似 2Sum Problem, 可以直接避免每次从 0 到 n-1 的遍历

```java
public int subarraySum(int[] nums, int k) {
    int n = nums.length, res = 0, sum = 0;
    Map<Integer, Integer> prefix = new HashMap<>();
    prefix.put(0, 1);
    
    for(int i = 0; i < n; i++){
        sum += nums[i];
        if(prefix.containsKey(sum - k)){
            res += prefix.get(sum - k);
        }
        prefix.put(sum, prefix.getOrDefault(sum, 0) + 1);
    }

    return res;
}
```

## 1074. Number of Submatrices That Sum to Target
https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/

基本思路依然是 presum + 2sum, 但是双PreSum. 

先 计算 each row 的 preSum, 然后使用 两列[i, j], i = [0, m], j = [i, m], 作为矩形的左右边界, 然后累加 each row 来计算在 列为[i, j] 的矩阵下, 竖着的 PreSum.

同理可以使用 each col 的 preSum, 然后遍历上下界 而不是 左右界

这种 双PreSum 的办法, 可以快速计算 matrix 中的每个矩阵的面积 和 相关信息, 复杂度 O(m * n^2)

```java
public int numSubmatrixSumTarget(int[][] matrix, int target) {
    // preSum for each Row
    for (int i = 0; i < matrix.length; i++) {
        for (int j = 1; j < matrix[0].length; j++) {
            matrix[i][j] += matrix[i][j - 1];
        }
    }
    
    int res = 0;
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < matrix[0].length; i++) {        // 左边界
        for (int j = i; j < matrix[0].length; j++) {    // 右边界
            map.clear();
            map.put(0, 1);
            int cur = 0;
            for (int k = 0; k < matrix.length; k++){    // 下边界
                // cur -= matrix[k][i-1], 去掉左边界以左的值
                cur += matrix[k][j] - (i > 0 ? matrix[k][i-1] : 0); 
                res += map.getOrDefault(cur - target, 0);
                map.put(cur, map.getOrDefault(cur, 0) + 1);
            }
        }
    }
    
    return res;
}
```




## 363. Max Sum of Rectangle No Larger Than K
https://leetcode.com/problems/max-sum-of-rectangle-no-larger-than-k/

PreSum 的老套路, 遍历所有行的组合, 将问题退化为 1D arr. 问题变为 最和不超过 k 的子数组的和, 把所有的 preSum 装进 TreeSet , 用 ceiling 查找左侧仅次于 sum - k 的最大值 prev. 如果存在值, 说明区间有效, 再用 sum - prev 计算区间和即可

```java
public int maxSumSubmatrix(int[][] matrix, int k) {
    int row = matrix.length, col = matrix[0].length, res = Integer.MIN_VALUE;
    for (int startRow = 0; startRow < row; startRow++) {
        int[] colSum = new int[col];
        for (int endRow = startRow; endRow < row; endRow++) {
            for (int i = 0; i < col; i++) {
                colSum[i] += matrix[endRow][i];
            }
            res = Math.max(res, findSum(colSum, k));
        }
    }
    return res;
}

private int findSum (int[] colSum, int k) {
    TreeSet<Integer> ts = new TreeSet<>();
    ts.add(0);
    int max = Integer.MIN_VALUE, sum = 0;
    for (int i = 0; i < colSum.length; i++) {
        sum += colSum[i];
        Integer prev = ts.ceiling(sum - k);
        if (prev != null)
            max = Math.max(max, sum - prev);
        ts.add(sum);
    }
    return max;
}
```

## 2420. Find All Good Indices
https://leetcode.com/problems/find-all-good-indices/

以 [k, n-k) 为中心, 左侧 k 个数字要求单减, 右侧 k 个数字要求单增.    
使用 prefixLen + suffixLen 实现快速访问趋势. 前后缀思想类似Kadane, 如果当前 num >= prev num, 则 在单增中, dp[i] = dp[i-1] + 1, 否则为 1.

然后遍历 + 快速访问即可

```java
public List<Integer> goodIndices(int[] nums, int k) {
    // i in [k, n-k), 在该范围内， 找小山谷
    List<Integer> res = new ArrayList<>();
    
    if (nums == null || nums.length == 0)
        return res;
    
    int n = nums.length;
    int[] nonInc = new int[n+1];
    int[] nonDec = new int[n+1];
    
    Arrays.fill(nonInc, 1);
    Arrays.fill(nonDec, 1);
    
    // 非增前缀长度和
    for (int i = 1; i < n; i++) {
        if (nums[i-1] >= nums[i])
            nonInc[i] = nonInc[i-1]  + 1;
    }
    // 非减后缀长度和
    for (int i = n - 2; i >= 0; i--) {
        if (nums[i+1] >= nums[i]) 
            nonDec[i] = nonDec[i + 1] + 1;
    }
    
    for (int i = k; i < n - k; i++) {
        if (nonInc[i-1] >= k && nonDec[i+1] >= k)
            res.add(i);
    }

    return res;
}
```

## 1352. Product of the Last K Numbers
https://leetcode.com/problems/product-of-the-last-k-numbers/

前缀乘积 + 特殊情况冲洗. 简单的前缀乘积, 但是当 0 来了, 要把前缀清空, 
1. 因为不清空, 纯维护会爆栈 
2. 任何包含 0 的乘积都是 0, 则在retrieve的时候, 如果 k > size 则返回 0 即可

```java
class ProductOfNumbers {
    List<Integer> products;
    public ProductOfNumbers() {
        products = new ArrayList<>();
        products.add(1);
    }
    
    public void add(int num) {
        if (num == 0) {
            products.clear();
            products.add(1);
            return;
        }
        
        if (products.isEmpty()) {
            products.add(num);
            return;
        }
            
        products.add(products.get(products.size() - 1) * num);
    }
    
    public int getProduct(int k) {
        return k < products.size() ? products.get(products.size() - 1) / products.get(products.size() - k - 1) : 0;
    }
}
```