# 最小生成树
计算最小生成树, 给定了 一组Node 和 Node的Edge集合, 找到最小的树, 满足所有Node都是相连的, 常用算法: Prim 和 Kruskal

算法链接: https://www.cnblogs.com/biyeymyhjob/archive/2012/07/30/2615542.html

## Prim 算法
时间复杂度: 邻接矩阵:O(v2), 邻接表:O(elog2v)
1. 首先创建 start node 到其所有邻接 node 的 cost矩阵 (lowCost) 和 访问与否矩阵 (vNew); 在 lowCost 中, 记录所有顶点到 start node 的最小距离; 在 vNew 中, 记录 node 是否访问过, -1 表示未访问, 0 表示已访问.
2. 遍历 n - 1 次构造树, 因为: 初始node; 进入 loop 时, lowCost 是更新过后, 遍历所有 node, 找到 **距离 当前 生成树最近**的 node, 记录其距离 min 和 v;
3. 如果存在这个 node: 在 vNew 中记录, node v 已访问; 总距离 += min;
4. 根据这个最新加入的 node, **更新 当前生成树 到 其余节点 的距离**, 

adj 记录 其index node 与 哪个 node 相连

```java
public int primAlgo(int[][] edges, int start) {
    int n = edges.length;
    int[] lowCost = new int[n];
    int[] vNew = new int[n];
    int[] adj = new int[n];
    int weightSum = 0;
    int i = 0, j = 0, k = 0;
    
    // start 顶点的cost list
    for(i = 0; i < n; i++){
        lowCost[i] = edge[start][i];
        vNew[i] = -1; 
    }
    vNew[start] = 0;
    adj[start] = 0;
    
    for(i = 0; i < n - 1; i++){
        int min = Integer.MAX_VALUE;
        int v = -1;
        for(j = 0; j < n; j++){
            // 查询 未访问的节点 的 cost 是否更小
            if(vNew[j] == -1 && lowCost[j] < min){
                min = lowCost[j];
                v = j;
            }
        }
        if(v != -1){
            vNew[v] = 0;
            weightSum += min;
            for(j = 0; j < n; j++){
                // 节点未访问, 且距离小于当前, 更新
                if(vNew[j] == -1 && edge[v][j] < lowCost[j]){
                    lowCost[j] = edge[v][j];
                    adj[j] = v;
                }
            }
        }
    }   
    return weightSum;
}

```



## Kruskal 算法


1584. Min Cost to Connect All Points: https://leetcode.com/problems/min-cost-to-connect-all-points/

构建邻接矩阵 或者 邻接表 套用算法即可


```java
public int minCostConnectPoints(int[][] points) {
    int n = points.length;
    int[][] edge = new int[n][n];
    int[] lowCost = new int[n];
    int[] vNew = new int[n];
    int[] adj = new int[n];
    
    // generate adj matrix
    for(int i = 0; i < n; i++){
        for(int j = 0; j < n; j++){
            edge[i][j] = Math.abs(points[i][0] - points[j][0]) + Math.abs(points[i][1] - points[j][1]);
        }
    }
    
    int weightSum = 0;
    int i = 0, j = 0, k = 0;
    
    for(i = 0; i < n; i++){
        lowCost[i] = edge[0][i];
        vNew[i] = -1; 
    }
    vNew[0] = 0;
    adj[0] = 0;
    
    for(i = 0; i < n - 1; i++){
        int min = Integer.MAX_VALUE;
        int v = -1;
        for(j = 0; j < n; j++){
            if(vNew[j] == -1 && lowCost[j] < min){
                min = lowCost[j];
                v = j;
            }
        }
        if(v != -1){
            vNew[v] = 0;
            weightSum += lowCost[v];
            for(j = 0; j < n; j++){
                if(vNew[j] == -1 && edge[v][j] < lowCost[j]){
                    lowCost[j] = edge[v][j];
                    adj[j] = v;
                }
            }
        }
    }   
    return weightSum;
}
```

