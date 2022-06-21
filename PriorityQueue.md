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

## 1642. Furthest Building You Can Reach
https://leetcode.com/problems/furthest-building-you-can-reach/

不能使用DP, 不存在最优子问题, 因为一个是 sum - val, 一个是 # - 1. 最优子结构存在重叠

使用 Greedy, 存储最大的jump, 退化为 Kth largerest element 问题

```java
public int furthestBuilding(int[] heights, int bricks, int ladders) {
    if(heights == null || heights.length == 0)  return 0;
    
    PriorityQueue<Integer> pq = new PriorityQueue<>();    // store the largerest jumps
    int sum = 0, i = 0;
    for(; i < heights.length - 1; i++){
        if(heights[i] < heights[i+1]){
            int diff = heights[i+1] - heights[i];
            if(pq.size() < ladders){
                pq.offer(diff);
            }else{
                // find k-th largerest element in the array
                if(pq.isEmpty() || diff <= pq.peek()){
                    sum += diff;
                }else{
                    pq.offer(diff); // update largerer 
                    sum += pq.poll();
                }
            }
        }
        
        if(sum > bricks)    break;
    }
    return i;
}
```

## 2268. Minimum Number of Keypresses
https://leetcode.com/problems/minimum-number-of-keypresses/

set 记数, PQ排序, 排序完毕直接 poll * coefficient 即可

```java
public int minimumKeypresses(String s) {
    Map<Character, Integer> map = new HashMap<>();
    char[] chars = s.toCharArray();
    for(char c : chars){
        int count = map.getOrDefault(c, 0);
        map.put(c, count + 1);
    }
    PriorityQueue<Map.Entry<Character, Integer>> pq = new PriorityQueue<>((o1, o2) -> o2.getValue() - o1.getValue());
    for(Map.Entry<Character, Integer> e : map.entrySet()){
        pq.offer(e);
    }
    int res = 0, num = 0;
    while(!pq.isEmpty()){
        res += ((num / 9) + 1) * pq.poll().getValue();
        num++;
    }
    return res;
}
```