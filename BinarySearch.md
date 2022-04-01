# Binary Search

设置一个 high 和一个 low, high 从上界开始减少, low 从下界开始增加, 取二者中间值 mid = low + (high - low) / 2 和目标比较, 注意边界条件: high >= low 使得high low 相等时也能访问到中间值, high = mid - 1 和 low = mid + 1 在 low = 0, high = 2, mid = 2时能跳出死循环 

除了 mid 和目标相同时退出, 常用退出条件还有一个就是 low, 原因: 当 low = mid + 1 时, 搜索范围在 从右往左 缩小, high 作为 < low 的退出条件是会等于 mid , 所以选择 low (保底)

Binary Search: https://leetcode.com/problems/binary-search/

经典模板

```
 public int search(int[] nums, int target) {
     int lo = 0, hi = nums.length - 1;
     while(hi >= lo){
         int mid = lo + (hi - lo) / 2;
         if(nums[mid] > target){
             hi = mid - 1;
         }else if(nums[mid] < target){
             lo = mid + 1;
         }else{
             return mid;
         }
     }
     return -1;
 }
```

The K Weakest Rows in a Matrix: https://leetcode.com/problems/the-k-weakest-rows-in-a-matrix/

使用二分搜索找到最后一个 1 + 使用 pq 和正则表达式做最小堆(可以对min heap内部分情况讨论 (a, b) -> (a[0] == b[0] ? a[1] - b[1] : a[0] - b[0])). 优化: 反向实现, 使用最大堆, 直接把最大的去掉, 只保留k个最小的,最后在保存至array 反向保存即可

```java
public int[] kWeakestRows(int[][] mat, int k) {
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> (a[1] == b[1] ? b[0] - a[0] : b[1] - a[1]));
    for(int i = 0; i < mat.length; i++){
        pq.offer(new int[]{i, countOnes(mat[i])});
        if(pq.size() > k)   pq.poll();
    }
    int[] res = new int[k];
    while(k>0)  res[--k] = pq.poll()[0];
    return res;
}

private int countOnes(int[] row){
    int hi = row.length-1, lo = 0;
    while(hi >= lo){
        int mid = lo + (hi-lo)/2;
        if(row[mid] == 1){
            lo = mid + 1;
        }else{
            hi = mid - 1;
        }
    }
    return lo;
}
```

Search in Rotated Sorted Array II: https://leetcode.com/problems/search-in-rotated-sorted-array-ii/

二分搜索, 因为 nums 分为两段, 所以 mid 有 4个落点: 

首先判断 mid 落在 左右 半区, n[mid] > n[lo] 则在左半区 : 右半区

然后根据 n[mid] 和 target 判断, target 在 n[mid] 的左右 

Trick: 使用 while(n[lo] == n[lo+1]) lo++ 去重

```java
public boolean search(int[] nums, int target) {
    int n = nums.length;
    int hi = n-1, lo = 0;
    while(hi >= lo){
        while(hi > lo && nums[hi] == nums[hi - 1]) hi--;
        while(hi > lo && nums[lo] == nums[lo + 1]) lo++;
        
        int mid = lo + (hi-lo)/2;
        if(nums[mid] == target){
            return true;
        }else if(nums[mid] >= nums[lo]){
            if(nums[mid] > target && nums[lo] <= target){
                hi = mid - 1;
            }else{
                lo = mid + 1;
            }
        }else{
            if(target > nums[mid] && target <= nums[hi]){
                lo = mid + 1;
            }else{
                hi = mid - 1;
            }
        }
    }
    return false;
}
```

Search a 2D Matrix: https://leetcode.com/problems/search-a-2d-matrix/

标准二分, 但其实最简单的办法是作为一个 sorted array 对待

```java
public boolean searchMatrix(int[][] matrix, int target) {
    int n = matrix.length;
    if(n == 0)  return false;
    int m = matrix[0].length;
    
    int hiRow = n-1, loRow = 0, tarRow = 0;
    while(hiRow >= loRow){
        int mid = loRow + (hiRow - loRow) / 2;
        if(matrix[mid][0] <= target && matrix[mid][m-1] >= target){
            tarRow = mid;
            break;
        }else if(matrix[mid][0] > target){
            hiRow = mid - 1;
        }else{
            loRow = mid + 1;
        }
    }
    
    int lo = 0, hi = m - 1;
    while(hi>=lo){
        int mid = lo + (hi - lo) / 2;
        if(matrix[tarRow][mid] == target){
            return true;
        }else if(matrix[tarRow][mid] > target){
            hi = mid - 1;
        }else{
            lo = mid + 1;
        }
    }
    return false;
}
```



# 基础计算
Pow(x, n): https://leetcode.com/problems/powx-n/

计算pow, 方法: 二分思想, 每次都与自己相乘, 则有 2^5 * 2^5 = 2^10 如果遇到 odd, 则 额外再乘上一个 x 即可(因为除法向下取整 5/2 = 2, 1+2+2). 注意: n 的范围取正会越界使用 long 保存

```java
 public double myPow(double x, int n) {
     double res = 1;
     long m = n;
     if(m < 0){
         m = -m;
         x = 1/x;
     }
     while(m>0){
         if(m % 2 == 1){
             res *= x;
         }
         x *= x;
         m /= 2;
     }
     return res;
 }
```

# 经典隐藏上下界二分
## 典中典: 可可吃香蕉
Koko Eating Bananas: https://leetcode.com/problems/koko-eating-bananas/

上界: 理论是 sum(nums) 但是铁越界, 所以直接用MAX_VALUE; 下界: 1

check 方式: 看当前所需时间是否超过指定时间, 超过则说明慢了; 

一直保持 valid 在 [mid + 1, high] 的空间, 则 lo = mid + 1, 返回 lo 即可

```java
public int minEatingSpeed(int[] nums, int h) {
    int hi = Integer.MAX_VALUE, lo = 1;
    while(hi > lo){
        int mid = lo + (hi - lo) / 2;
        int count = 0;
        for(int num : nums) count += (int)Math.ceil(1.0 * num / mid);
        
        if(count > h){
            lo = mid + 1;
        }else{
            hi = mid;
        }
    }
    return lo;
}
```




Split Array Largest Sum: https://leetcode.com/problems/split-array-largest-sum/

上界: 数组和(m=1), 下界: 最大数组(m=n), high = 上界, low = 下界, 二分搜索可能值范围

使用 mid 作为最大的 subarray sum, count subarray 的数量, 如果 count 小于 m , 则说明这个 mid 可以再小一点, 还有子数组个数的余韵; 如果 count 大于 m, 说明 mid 不够大, subarray 超出了指定的 m

```java
public int splitArray(int[] nums, int m) {
    int hi = 0, lo = 0;
    for(int num : nums){
        hi += num;
        lo = Math.max(lo, num);
    }
    
    while(hi > lo){
        int mid = lo + (hi - lo) / 2;
        int total = 0, count = 1;
        for(int num : nums){
            if(total + num <= mid){
                total += num;
            }else{
                total = num;
                count += 1;
            }
        }
        if(count > m){
            lo = mid + 1;
        }else   hi = mid;
    }
    return hi;
}
```