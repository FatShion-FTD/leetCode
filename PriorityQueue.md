# PriorityQueue 优先队列

Last Stone Weight: https://leetcode.com/problems/last-stone-weight/

```java
public int lastStoneWeight(int[] nums) {
    PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
    for(int num : nums) pq.offer(num);
    while(pq.size() > 1){
        int stone1 = pq.poll();
        int stone2 = pq.poll();
        if(stone1 != stone2)    pq.offer(stone1 - stone2);
    }
    return pq.isEmpty() ? 0 : pq.poll();
}
```

Kth Largest Element in an Array: https://leetcode.com/problems/kth-largest-element-in-an-array/

```java
public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    for(int i = 0; i < k; i++){
        pq.offer(nums[i]);
    }
    for(int i = k; i < nums.length; i++){
        if(nums[i] <= pq.peek())    continue;
        pq.offer(nums[i]);
        pq.poll();
    }
    return pq.poll();
}
```

Top K Frequent Elements: https://leetcode.com/problems/top-k-frequent-elements/

核心: 使用 Map.Entry<Integer, Integer> 和 hm.entrySet() 作为 PQ 的element, 两个核心函数 getKey() 和 getValue()

```java
public int[] topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> hm = new HashMap<>();        
    for(int num : nums){
        hm.put(num, hm.getOrDefault(num, 0) + 1);
    }
    PriorityQueue<Map.Entry<Integer, Integer>> pq = new PriorityQueue<>((o1, o2) -> (o2.getValue() - o1.getValue()));
    for(Map.Entry<Integer, Integer> entry : hm.entrySet()){
        pq.offer(entry);
    }
    
    int[] res = new int[k];
    for(int i = 0; i < k; i++){
        res[i] = pq.poll().getKey();
    }
    return res;
}
```

Kth Largest Element in a Stream: https://leetcode.com/problems/kth-largest-element-in-a-stream/

使用 pq 记录 前 k 个元素, 如果 pq.size() 不满足 则直接加入, 否则 poll 了 再 offer

```java
class KthLargest {
    private int k;
    private PriorityQueue<Integer> pq;
    
    public KthLargest(int k, int[] nums) {
        this.k = k;
        pq = new PriorityQueue<>((o1, o2) -> (o1 - o2));    // min heap
        Arrays.sort(nums);      // store first k elements
        for(int i = nums.length - 1; i >= nums.length - k && i >= 0; i--){
            pq.offer(nums[i]);
        }
    }
    
    public int add(int val) {
        if(pq.size() < k){
            pq.offer(val);
            return pq.peek();
        }
        if(val <= pq.peek()) return pq.peek();
        pq.poll();
        pq.offer(val);
        return pq.peek();
    }
}
```