# BFS 广度优先遍历
QUEUE, 用好了就完事儿了

1.牵一发而动全身, 2. 每一层 size 很重要 3. 从初始开始, 只能 TOP-DOWN 



## 542. 01 Matrix
https://leetcode.com/problems/01-matrix/

BFS, 从 0 开始, 把所有 0 放入 Queue, 每次更新周围 4 个位置, 如果要更新的位置的 val 更小 则 continue; 否则 1 + mat[i][j], 并将该位置放入 Queue

```java
public int[][] updateMatrix(int[][] mat) {
    int n = mat.length, m = mat[0].length;
    int[] dirs = new int[]{0, -1, 0, 1, 0}; // 4 directions
    Deque<int[]> q = new ArrayDeque<>();
    
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (mat[i][j] == 0){
                q.offer(new int[]{i, j});
            }else{
                mat[i][j] = Integer.MAX_VALUE;
            }
        }
    }
    
    while (!q.isEmpty()) {
        int[] node = q.poll();
        for(int i = 1; i < dirs.length; i++){
            int x = node[0] + dirs[i-1], y = node[1] + dirs[i];
            if(x < 0 || x >= n || y < 0 || y >= m || 
                mat[x][y] <= 1 + mat[node[0]][node[1]])
                continue;
            q.offer(new int[]{x, y});
            mat[x][y] = 1 + mat[node[0]][node[1]];
        }
    }
    
    return mat;
}
```

## 787. Cheapest Flights Within K Stops
https://leetcode.com/problems/cheapest-flights-within-k-stops/

DFS, BFS 都 TLE. 所以用超直接算法, 也叫 **Bellman Ford Algo**.

创建一个距离数组 cost, 存起点到每个节点的最小距离.

遍历K次, 每次遍历所有edges, 每次更新所有有关节点的cost. 如果初始节点为 MAX_VALUE, 说明目前 该 node 和 src 不连通, 不更新(剪枝)

```java
public int findCheapestPrice(int n, int[][] flights, int src, int dst, int K) {
    int[] cost=new int[n];
    Arrays.fill(cost,Integer.MAX_VALUE);
    cost[src]=0;
    for(int i=0;i<=K;i++)
    {
        int[] temp= Arrays.copyOf(cost,n);
        for(int[] f: flights)
        {
            int curr=f[0],next=f[1],price=f[2];
            if(cost[curr]==Integer.MAX_VALUE)
                continue;
            temp[next]=Math.min(temp[next],cost[curr]+price);
        }
        cost=temp;
    }
    return cost[dst]==Integer.MAX_VALUE?-1:cost[dst];
}
```

