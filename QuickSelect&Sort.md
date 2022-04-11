# QuickSort 快排
选定一个 pivot, 把所有小于 pivot 的放左边, 大于的放右边

# QuickSelect   快速选择



Kth Largest Element in an Array: https://leetcode.com/problems/kth-largest-element-in-an-array/

https://leetcode.com/problems/kth-largest-element-in-an-array/discuss/60294/Solution-explained

**** 没搞懂呢 ****


四点: 1. 使用 

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        int n = nums.length;
        k = n - k;
        int lo = 0, hi = n - 1;
        while(hi > lo){
            int p = partition(nums, lo, hi);
            if(p < k){
                lo = p + 1;
            }else if( p > k){
                hi = p - 1;
            }else{
                break;
            }
        }
        return nums[k];
    }
    
    private int partition(int[] nums, int lo, int hi){
        int i = lo;
        int j = hi + 1;
        while(true){
            while(i < hi && nums[++i] < nums[lo]);  // 从左到右找第一个 > nums[lo]
            while(j > lo && nums[--j] > nums[lo]);  // 从右到左找第一个 < nums[lo]
            if(i >= j)  break;  
            exch(nums, i, j);
        }
        exch(nums, lo, j);
        return j;
    }
    
    private void exch(int[] nums, int i, int j){
        int tmp = nums[i];
        nums[i] = nums[j];
        nums[j] = tmp;
    }
    
    private void shuffle(int[] nums){
        Random random = new Random();
        for(int i = 1; i < nums.length; i++){
            int r = random.nextInt(i+1);
            exch(nums, i, r);
        }
    }
}
```