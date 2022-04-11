# Bucket Sort 桶排序
使用一个 Bucket 作为排序的办法:

把 数组 分为 有限的 bucket, 每个 bucket 都有特殊的意义, 如: 出现的次数, bucket本身也是一个 list, 里面存放着 具有上述意义的 对应值.

对 bucket 排序后, 再逐个访问 每个桶 中的元素


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