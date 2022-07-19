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





