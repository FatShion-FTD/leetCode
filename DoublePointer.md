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