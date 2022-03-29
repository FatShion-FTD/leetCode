# Find the Duplicate Number
https://leetcode.com/problems/find-the-duplicate-number/



经典例题, 三解法: 二分, 双指针, 位操作

## 二分

找中间点, count 所有**小于等于** mid 的值

如果 count 比 mid 多, 则重复的在mid 左边 [1, mid], 否则在 [mid+1, n] 

```java
public int findDuplicate(int[] nums) {
    int lo = 0, hi = nums.length - 1;
    while(hi > lo){
        int mid = lo + (hi-lo)/2;
        int count = 0;
        for(int num : nums){
            if(num <= mid)   count++;
        }
        if(count <= mid){
            lo = mid + 1;
        }else{
            hi = mid;
        }
    }
    return lo;
}
```

## 双指针

