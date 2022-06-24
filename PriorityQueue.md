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
        // less than the smallest one, ignore
        if(nums[i] <= pq.peek())    continue;
        // add new largerer one, remove smallest one
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
# Course Schedule 系列
## 207. Course Schedule
https://leetcode.com/problems/course-schedule/

问题抽象: 找到是否有 环形的 先修课, 如 (0,1) (1,0) -> 找到选修课中是否存在环

找环的算法: Topological Sort 拓扑排序. BFS 实现核心:
1. 维持一个 Graph, 存储 先修课 pre[1] -> 后修课pre[1] 的关系, 和 一个 indegree 用来存储每个 node 的入度
2. 从 indegree 中找到入度为 0 的 node, 并将其加入到 queue 中, 此时使用 BFS 的思路
3. 当 queue 不为空时, 取出 queue 的头元素, 删除所有和他相关的 edge, 即: 将其指向的 node 的 indegree - 1, 如果相关的 node indegree 为 0, 则加入到 queue 中
4. 记录访问 node 的次数 count, 如果所有 node 都访问到, 则 true

```java
public boolean canFinish(int n, int[][] prerequisites){
    int[] indegree = new int[n];
    List<List<Integer>> graph = new ArrayList<>();
    for(int i = 0; i < n; i++){
        graph.add(new ArrayList<>());
    }
    for(int[] p : prerequisites){
        indegree[p[1]]++;
        graph.get(p[0]).add(p[1]);
    }
    int count = 0;
    Queue<Integer> q = new LinkedList<>();
    for(int i = 0; i < n; i++){
        if(indegree[i] == 0) q.offer(i);
    }
    while(!q.isEmpty()){
        int cur = q.poll();
        count++;
        for(int next : graph.get(cur)){
            indegree[next]--;
            if(indegree[next] == 0) q.offer(next);
        }
    }
    return count == n;
}
```

## 630. Course Schedule III
https://leetcode.com/problems/course-schedule-iii/

贪心 + PQ: 跟据关系, 一门课程, 如果当前是OK的 time 和 end, 则他之前任何一门课程删除, 他依然是OK的
1. 先按照结束时间排序, 课程相对按照顺序上课; 然后创建一个 max heap 的 PQ, 用来存每门课程的 time
2. 总时间 tot 先加上 课程时间, 如果 tot < 当前课程的结束时间则没有问题; 如果 tot > cur End, 则把之前最大时常 time 的课程去掉即可

```java
public int scheduleCourse(int[][] courses) {
    // finish A Early AP
    Arrays.sort(courses, (o1, o2) -> o1[1] - o2[1]);
    PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
    int tot = 0;
    for(int[] c : courses){
        int time = c[0], end = c[1];
        tot += time;
        pq.offer(time);
        while(!pq.isEmpty() && tot > end)  tot -= pq.poll();
    }
    return pq.size();
}
```

