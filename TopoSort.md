# Topo Sort
找环的算法: Topological Sort 拓扑排序. BFS 实现核心:
1. 维持一个 Graph, 存储 先修课 pre[1] -> 后修课pre[1] 的关系, 和 一个 indegree 用来存储每个 node 的入度
2. 从 indegree 中找到入度为 0 的 node, 并将其加入到 queue 中, 此时使用 BFS 的思路
3. 当 queue 不为空时, 取出 queue 的头元素, 删除所有和他相关的 edge, 即: 将其指向的 node 的 indegree - 1, 如果相关的 node indegree 为 0, 则加入到 queue 中
4. 记录访问 node 的次数 count, 如果所有 node 都访问到, 则 true


## 207. Course Schedule
https://leetcode.com/problems/course-schedule/

问题抽象: 找到是否有 环形的 先修课, 如 (0,1) (1,0) -> 找到选修课中是否存在环

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

## 2392. Build a Matrix With Conditions
https://leetcode.com/problems/build-a-matrix-with-conditions/

为什么用Topo: 要求相对位置. 跟据入度情况可以知道 有多少个 数字 在当前数字的 左边 / 上边 (前序依赖), 没有任何依赖的就在最下面, 然后对其所有相关节点, 去掉其依赖, 当存在新的节点也没有依赖的时候, 接着放置. 

放置是倒序的, 因为依赖从少到多.

```java
 public int[][] buildMatrix(int k, int[][] rowConditions, int[][] colConditions) {
     int[] row = new int[k + 1], col = new int[k + 1];
     if (topoSort(k, rowConditions, row) && topoSort(k, colConditions, col)) {
         int[][] res = new int[k][k];
         for (int i = 1; i <= k; i++) {
             res[row[i]][col[i]] = i;
         }
         return res;
     }
     return new int[0][0];
 }
 
 private boolean topoSort(int k, int[][] conditions, int[] mat) {
     int[] indegree = new int[k + 1];
     List<List<Integer>> g = new ArrayList<>();
     for (int i = 0; i <= k; i++)
         g.add(new ArrayList<>());
         
     for (int[] c : conditions) {                        // 构建adj graph
         int start = c[0], end = c[1];
         List<Integer> list = g.get(end);
         list.add(start);
         indegree[start]++;
     }
     
     Deque<Integer> q = new ArrayDeque<>();
     for (int i = 1; i <= k; i++) {                      // 起点入入度为0
         if (indegree[i] == 0) {
             q.push(i);
         }
     }
     
     int index = k - 1;                                  // 开始的点在最右 | 下
     while (!q.isEmpty()) {
         int node = q.poll();
         mat[node] = index--;
         for (int n : g.get(node)) {
             if (--indegree[n] == 0) {                   // 抹了来自 node 的入度, 如果此时 n 不再产生依赖, 则放置
                 q.offer(n);
             }
         }
     }
     
     return index == -1;
 }
```