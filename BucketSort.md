# Bucket Sort 桶排序
使用一个 Bucket 作为排序的办法:

把 数组 分为 有限的 bucket, 每个 bucket 都有特殊的意义, 如: 出现的次数, bucket本身也是一个 list, 里面存放着 具有上述意义的 对应值.

对 bucket 排序后, 再逐个访问 每个桶 中的元素

## Count Sort
记数排序就是桶排序的基础, 比如 0 - 10, 创建 10 个桶, 把0 - 10分别放到各自的桶里面即可

```java
public void countSort(int[] arr) {
    int n = arr.length, max = getMax(arr);
    int[] buckets = new int[max + 1];
    for (int num : arr) {
        buckets[num]++;
    }
    int index = 0;
    for (int i = 0; i < max + 1; i++) {
        while (bucket[i] != 0) {
            arr[index++] = i;
            bucket[i]--;
        }
    }
    return arr;
}
```


## Bucket Sort
## 164. Maximum Gap
https://leetcode.com/problems/maximum-gap/

![aaa](https://upload.wikimedia.org/wikipedia/commons/3/39/Bucket_sort_2.png)

标准的桶排序, 先找到最大值和最小值. 计算每个桶的大小 bucketSize 和 桶的数量 bucketNum, 创建桶. 对于每个数字, 计算其值 到 最小值的 shift index, 并装入相应的桶. 第 K 个桶, 包含 [min + (k-1) * gap, min + k * gap) 的值. 记录每个桶的最小值和最大值即可, 用当前桶的最小值减去上个桶的最大值就是答案.

因为桶内的 gap 都会小于 bucketSize, 最大gap 只会出现在 邻接两桶之间

```java
class Solution {
    public int maximumGap(int[] nums) {
        // O(n) O(n), bucket sort
        int max = Integer.MIN_VALUE, min = Integer.MAX_VALUE;
        for (int num : nums) {
            max = Math.max(num, max);
            min = Math.min(num, min);
        }
        int n = nums.length;
        if (nums == null || n < 2)
            return 0;
        
        int bucketSize = Math.max(1, (max - min) / (n-1));
        int bucketNum = (max - min) / bucketSize + 1;
        Bucket[] bueckets = new Bucket[bucketNum];
        
        for (int num : nums) {
            int index = (num - min) / bucketSize;
            if (bueckets[index] == null)
                bueckets[index] = new Bucket();
            bueckets[index].min = Math.min(num, bueckets[index].min);
            bueckets[index].max = Math.max(num, bueckets[index].max);
        }
        
        int prev = min, maxGap = 0;
        for (Bucket b : bueckets) {
            if (b == null)
                continue;
            
            maxGap = Math.max(maxGap, b.min - prev);
            prev = b.max;
        } 
        return maxGap;
    }
    
    public class Bucket {
        int min = Integer.MAX_VALUE;
        int max = Integer.MIN_VALUE;
        public Bucket() {}
    }
}
```

## Problems
## Top K Frequent Elements 
https://leetcode.com/problems/top-k-frequent-elements/

使用 bucket 装属于 出现次数 的值, 如 出现了100次的1 和 100次的2, 都放在 bucket[100] 中, 从装的最多的桶开始访问, 构建答案

```java
public int[] topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> hm = new HashMap<>();        
    for(int num : nums){
        hm.put(num, hm.getOrDefault(num, 0) + 1);
    }
    List<Integer>[] bucket = new List[nums.length + 1];
    for(int n : hm.keySet()){
        int freq = hm.get(n);
        if(bucket[freq] == null)    bucket[freq] = new LinkedList<>();
        bucket[freq].add(n);
    }
    
    int[] res = new int[k];
    int index = 0;
    for(int i = bucket.length - 1; i > 0; i--){
        if(bucket[i]!=null){
            List<Integer> list = bucket[i];
            for(int j = 0; j < list.size() && index < k; j++){
                res[index++] = list.get(j);
            }
        }
    }
    return res;
}
```

## 1874. Minimize Product Sum of Two Arrays 
https://leetcode.com/problems/minimize-product-sum-of-two-arrays/

简单的方法是使用PQ, 但是 桶排序在这道问题有奇效, 原因: num 的取值有特殊性: 0 - 100

这意味着存在使用 桶排序 的可能, 空间复杂度 O(100) 够低, 只需要创建100个桶并把他们逐个放入, 最后 rebuild sorted array 即可

```java
public int minProductSum(int[] nums1, int[] nums2) {
    int[] count1 = new int[101], count2 = new int[101];
    for(int num : nums1)count1[num] += 1;
    for(int num : nums2) count2[num] += 1;
    
    int p1 = 0, p2 = 100, i1 = 0, i2 = 0;
    while(p1<=100 && p2 >= 0){
        if(count1[p1] != 0){
            while(count1[p1] > 0){
                nums1[i1++] = p1;
                count1[p1]--;
            }
        }
        p1++;
        if(count2[p2] != 0){
            while(count2[p2] > 0){
                nums2[i2++] = p2;
                count2[p2]--;
            }
        }
        p2--;
    }
    int res = 0;
    for(int i = 0; i < nums1.length; i++){
        res += nums2[i] * nums1[i];
    }
    return res;
}
```