# Union Find
```java
class UnionFind {
    private int[] root;
    // Use a rank array to record the height of each vertex, i.e., the "rank" of each vertex.
    private int[] rank;
    private int count;

    public UnionFind(int size) {
        root = new int[size];
        rank = new int[size];
        count = size;
        for (int i = 0; i < size; i++) {
            root[i] = i;
            rank[i] = 0; 
        }
    }
	// The find function here is the same as that in the disjoint set with path compression.
    public int find(int x) {
        if (x == root[x]) {
            return x;
        }
        return root[x] = find(root[x]);
    }
	// The union function with union by rank
    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            if (rank[rootX] > rank[rootY]) {
                root[rootY] = rootX;
            } else if (rank[rootX] < rank[rootY]) {
                root[rootX] = rootY;
            } else {
                root[rootY] = rootX;
                rank[rootX] += 1;
            }
            count--;
        }
    }
    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
    public int getCount(){
        return count;
    }
}
```

## Number of Provinces: https://leetcode.com/problems/number-of-provinces/

经典例题, 直接连接使用 UF 即可


## Redundant Connection: https://leetcode.com/problems/redundant-connection/

isConnected函数的经典应用

```java
public int[] findRedundantConnection(int[][] edges) {
    int n = edges.length;
    UnionFind uf = new UnionFind(n+1);
    for(int i = 0; i < n; i++){
        int[] pair = edges[i];
        if(uf.isConnected(pair[0], pair[1]))   return pair;
        uf.union(pair[0], pair[1]);
    }
    return edges[n-1];
}
```

Number of Islands: https://leetcode.com/problems/number-of-islands/

经典例题, 同时包含了矩阵判断, 核心: 注意在 j > 0 且 i > 0 的时候, 上左判断同时进行, 先上后左, 进行有效联通

```java
public int numIslands(char[][] grid) {
    int n = grid.length;
    if(n == 0)  return 0;
    int m = grid[0].length;
    
    UnionFind uf = new UnionFind(m * n);
    for(int i = 0; i < n; i++){
        for(int j = 0; j < m; j++){
            if(grid[i][j] == '0'){
                uf.deCount();
            }else{
                if(i == 0){
                    if(j > 0 && grid[i][j-1] == '1')    uf.union(j-1, j);
                }else{
                    if(grid[i-1][j] == '1')     uf.union(i*m+j, (i-1)*m+j);
                    if(j > 0 && grid[i][j-1] == '1')    uf.union(i*m+j, i*m+j-1);
                }
            }
        }
    }
    return uf.getCount();
}
```

Smallest String With Swaps: https://leetcode.com/problems/smallest-string-with-swaps/

核心: UnionFind + 使用 MAP 搭配 PriorityQueue 实现根据 root 分类的自动排序 char 队列

问题主要在于: 如何储存 联通分量 并 按照字典序排列, 连通分量使用 Map, key 是 root, value 是这个连通分量下所有的连通点, 字典序排列 使用 PriorityQueue 即可, 构建完毕即可 使用 map.get(find(i)).poll() 逐个访问


```java
public String smallestStringWithSwaps(String s, List<List<Integer>> pairs) {
    int n = s.length();
    UnionFind uf = new UnionFind(n);
    for(List<Integer> pair : pairs){
        uf.union(pair.get(0), pair.get(1)); // 记录联通的indexs
    }
    Map<Integer, PriorityQueue<Character>> hm = new HashMap<>();
    for(int i = 0; i < n; i++){
        char c = s.charAt(i);
        int root = uf.find(i);
        hm.putIfAbsent(root, new PriorityQueue<>());
        hm.get(root).offer(c);
    }
    StringBuilder sb = new StringBuilder();
    for(int i = 0; i < n; i++){
        sb.append(hm.get(uf.find(i)).poll());
    }
    return sb.toString();
}
```
1631. Path With Minimum Effort: https://leetcode.com/problems/path-with-minimum-effort/

二分查找+连通问题, 和coco banana类似, 使用 UnionFind 作为判断二分的标准


```java
public int minimumEffortPath(int[][] heights) {
    int low = 0, high = 1000001;
    while(high > low){
        int mid = low + (high - low) / 2;
        if(connect(mid, heights)){
            high = mid;
        }else{
            low = mid + 1;
        } 
    }
    return low;
}

private boolean connect(int thold, int[][] heights){
    int n = heights.length;
    int m = heights[0].length;
    UnionFind uf = new UnionFind(n*m);
    for(int i = 0; i < n; i++){
        for(int j = 0; j < m; j++){
            int curr = heights[i][j];
            if(j+1< m && Math.abs(curr - heights[i][j+1]) <= thold) uf.union(i*m+j, i*m + j+1); // right
            if(i+1< n && Math.abs(curr - heights[i+1][j]) <= thold) uf.union(i*m+j, (i+1)*m + j); // bottom
        }
    }
    return uf.connected(0, n*m-1);
}
```
785. Is Graph Bipartite?: https://leetcode.com/problems/is-graph-bipartite/

Bipartite的核心: 两个graph子集, 内部没有连通.

使用UnionFind, 遍历 每个node 并查找其 parent(检查是否与别的分量连通), 遍历其 子分量, 将所有子分量进行连通, 如果和子分量有连通则 不行.

原因: 每次访问一个子graph, 相当于把 当前 node 和 其子连通分量 当作两个 set, 这两个绝对不能有重合. 而使用 find 可以保证哪怕在非当次连通的时刻, 也能保证其和子分量的重合会被打找到

```java
public boolean isBipartite(int[][] graph) {
  int n = graph.length;
  UnionFind uf = new UnionFind(n);
  for(int i = 0; i < n; i++){
      int part = uf.find(i);  
      for(int j : graph[i]){
          if(uf.find(j) == part)   return false;
          uf.union(graph[i][0], j);   
      }
  }
  return true;
}
```
399. Evaluate Division: https://leetcode.com/problems/evaluate-division/

究极复杂, String型 + 带值 UnionFind: 使用 Map 记录UnionFind的 String Pair, 使用 Map 记录每个 变量除以其父变量 对应的值 

```java
class Solution {
    Map<String, String> p = new HashMap();  // UnionFind 的 p, <x, px>
    Map<String, Double> vals = new HashMap();   // 每个union的对应值, <x, val - x->px>
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        // unionfind check connected, undirected graph
        double[] res = new double[queries.size()];
        for(int i = 0; i < values.length; i++){
            union(equations.get(i).get(0), equations.get(i).get(1), values[i]);
        }
        for(int i = 0; i < queries.size(); i++){
            String x = queries.get(i).get(0), y = queries.get(i).get(1);
            res[i] = (p.containsKey(x) && p.containsKey(y) && find(x) == find(y)) ? vals.get(x) / vals.get(y): -1.0;
        }
        return res;
    }
    
    public void union(String x, String y, double v){
        add(x);
        add(y);
        String px = find(x), py = find(y);
        p.put(px, py);
        vals.put(px, v * vals.get(y)/ vals.get(x));
    }
    
    public String find(String x){
        String px = p.getOrDefault(x, x);
        if(px != x){    // 如果 x != px
            String pp = find(px);
            vals.put(x, vals.get(x) * vals.get(px));    
            p.put(x, pp);
        }
        return p.getOrDefault(x, x);
    }
    
    public void add(String x){
        if(p.containsKey(x))    return;
        p.put(x, x);    // p[x] = x
        vals.put(x, 1.0);   // v[x] = 1
    }
}
```

## 2316. Count Unreachable Pairs of Nodes in an Undirected Graph
https://leetcode.com/problems/count-unreachable-pairs-of-nodes-in-an-undirected-graph/

除了使用 UnionFind 把连通分量 group 到一起, 使用 HashMap 存 Group 和 count, 注意使用 long 避免 int 溢出

```java
    public long countPairs(int n, int[][] edges) {
        UnionFind uf = new UnionFind(n);
        for(int[] e : edges){
            uf.union(e[0], e[1]);
        }
        Map<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < n; i++)  
            map.put(uf.find(i), map.getOrDefault(uf.find(i), 0) + 1);
        long res = 0;
        for(Integer key : map.keySet()){
            long num = map.get(key);
            res += num * (n-num); 
        }
        return res/2;
    }
```