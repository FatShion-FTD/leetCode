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