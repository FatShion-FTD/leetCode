# Find the Duplicate Number
https://leetcode.com/problems/find-the-duplicate-number/

常规思路: sort + 前后相同 或者 hashmap / int[] 存访问过的element

但是 O(1) space 且 不能 modify array

鸽笼原理: 10个鸽子 塞 9个笼子, 一定有个笼子塞俩, 找那塞俩鸽子的笼子的大致方位即可

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

因为 存在重复数字, 使用 index 跳转时候, 会出现 从两个不同的数字 跳到 相同 index 的情况, 如 从 [2,1,3,2] , 当 index = 0 和 3 时 都跳到 index = 2 的 3, 此时入环

使用快慢指针, 让 slow = nums[slow] 和 fast = nums[nums[fast]] 直到二者相等

但此时 slow 和 fast 并不是那个重复的数字, 需要一个 find 从 0 开始模拟 入环的过程, 直到二者相等

思路来自: Linked List Cycle II: https://leetcode.com/problems/linked-list-cycle-ii/

```java
public int findDuplicate(int[] nums) {
    int slow = 0, fast = 0, find = 0;
    while(true){
        slow = nums[slow];
        fast = nums[nums[fast]];
        if(slow == fast)    break;
    }
    while(true){
        slow = nums[slow];
        find = nums[find];
        if(slow == find)    break;
    }
    return find;
}
```

## Bit 

从0到31遍历位数, 在每一 bit 让 index, num[index] 两者同时和 bit 做 AND, 通过比较二者的 1 的个数的不同, 判断 res 在 bit位 是否是1, 然后把各 bit 的 1 加到一起, 得到 res.

原理: 0 到 n-1 中所有数字中该 bit 上的 1 的个数应该是固定的，如果 nums 数组中所有数字中该位上1的个数多了，说明重复数字在该位上一定是1

```java
public int findDuplicate(int[] nums) {
    int res = 0;
    for(int i = 0; i < 32; i++){
        int bit = 1 << i, count1 = 0, count2 = 0;
        for(int i = 0; i< nums.lenght: i++){
            if(i & bit) count1++;
            if(nums[i] & bit)   count2++;
        }
        if(count2 > count1) res+= bit;
    }
    return res;
}
```