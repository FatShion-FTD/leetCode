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