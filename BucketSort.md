# Bucket Sort 桶排序
使用一个 Bucket 作为排序的办法:

把 数组 分为 有限的 bucket, 每个 bucket 都有特殊的意义, 如: 出现的次数, bucket本身也是一个 list, 里面存放着 具有上述意义的 对应值.

对 bucket 排序后, 再逐个访问 每个桶 中的元素

## 经典 Bucket Sort
对一个数组进行排序, 思路: 1. 先创建10个桶, 代表着当前位数从 0-9 的桶.

2. 从最低位数开始遍历直到最高位数: 对于每一位, 把 num 放入对应的 桶 中, 然后将所有数从 0桶 开始逐个 pop, 构建全新数组, 然后进入下一位的 sort 

## Problems
Top K Frequent Elements: https://leetcode.com/problems/top-k-frequent-elements/

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