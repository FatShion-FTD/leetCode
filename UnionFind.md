# Union Find
```java
class UnionFind {
    private int[] root;
    // Use a rank array to record the height of each vertex, i.e., the "rank" of each vertex.
    private int[] rank;
    // count the size of each component
    private int[] size;
    private int count;

    public UnionFind(int size) {
        root = new int[size];
        rank = new int[size];
        this.size = new int[size];
        count = size;
        for (int i = 0; i < size; i++) {
            root[i] = i;
            rank[i] = 0; 
            this.size[i] = 1;
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
    public int union(int x, int y) {
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
                // Update the size of the component by adding the size of subcomponent.
                size[rootX] += size[rootY];
            }
            count--;
        }
        return size[rootX];
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
## 128. Longest Consecutive Sequence
https://leetcode.com/problems/longest-consecutive-sequence/

把连续的 sequence 当作 disjoint component, 使用 MAP 记录 <num, index> 其中 index 表示 num 在数组中的位置, 使用 UnionFind 记录连通分量, 通过魔改 UnionFind 的 Union 函数 + 加以 size 变量, 可以让其记录连通分量的大小


```java
public int longestConsecutive(int[] nums) {
   if(nums.length <= 1)    return nums.length;
   
   UnionFind uf = new UnionFind(nums.length);
   Map<Integer, Integer> map = new HashMap<>();
   int max = 1;
   for(int i = 0; i < nums.length; i++){
       int num = nums[i], left = num - 1, right = num + 1;;
       if(map.containsKey(num))    continue;

       if(map.containsKey(left))
           max = Math.max(max, uf.union(i, map.get(left)));
       
       if(map.containsKey(right))
           max = Math.max(max, uf.union(i, map.get(right)));
       
       map.put(num, i);
   }
   
   return max;
}
// 魔改 UNION
public int union(int x, int y){
    int pX = find(x), pY = find(y);
    if(pX != pY){
        p[pY] = pX;
        size[pX] += size[pY];
    }
    return size[pX];
}
```

## 236. Lowest Common Ancestor of a Binary Tree
https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/

树形 UnionFind, 使用 Map 记录 <node, parent> 根据 parent map 构建路径, 取公共节点

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
   Map<TreeNode, TreeNode> map = new HashMap<>();
   Deque<TreeNode> stack = new ArrayDeque<>();
   stack.push(root);
   
   while(!stack.isEmpty()) {
       TreeNode node = stack.pop();
       if (node.left != null) {
           map.put(node.left, node);
           stack.push(node.left);
       }
       if (node.right != null) {
           map.put(node.right, node);
           stack.push(node.right);
       }
   }
   Set<TreeNode> set = new HashSet<>();
   while (p != null) {
       set.add(p);
       p = map.getOrDefault(p, null);
   }
   while (q != null) {
       if (set.contains(q))
           return q;
       q = map.getOrDefault(q, null);
   }
   return null;
}
```

## 130. Surrounded Regions
https://leetcode.com/problems/surrounded-regions/

又有一种新的技巧, UnionFind 的 dummy ptr. 把 p[n] 作为 dummy, 并把所有有特殊需求 (主要为不与图中任意元素连接, 或者有特殊链接要求) 的 ptr union 到这个 dummy, 实现具有 区别对待的 unionfind.

```java
class Solution {
    final static int[] DIRS = new int[]{-1, 0, 1, 0, -1};
    
    public void solve(char[][] b) {
        if (b == null)
            return;
        
        int m = b.length, n = b[0].length;
        UnionFind uf = new UnionFind(m * n);
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 || j == 0 || i == m - 1 || j == n - 1 && b[i][j] == 'O') 
                    uf.union(i * n + j, m * n);
                
                else if (b[i][j] == 'O') {
                    for (int k = 1; k < DIRS.length; k++) {
                        int row = i + DIRS[k - 1], col = j + DIRS[k];
                        if (row >= 0 && row < m && col >= 0 && col < n && b[row][col] == 'O')
                            uf.union(i * n + j, row * n + col);
                    }
                }
            }
        }
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (b[i][j] == 'O' && uf.find(i * n + j) != uf.find(m * n))
                    b[i][j] = 'X';
            }
        }
        
    }
    
    class UnionFind {
        int[] p;
        int[] r;
        int size;
        
        public UnionFind (int n) {
            this.size = n;
            p = new int[n + 1];
            r = new int[n + 1];
            for (int i = 0; i <= n; i++) {
                p[i] = i;
                r[i] = 0;
            }
        }
        
        public int find (int x) {
            if (p[x] == x)
                return x;
            return p[x] = find(p[x]);
        }
        
        public void union (int x, int y) {
            int px = find(x);
            int py = find(y);
            if (px != py) {
                if (r[px] > r[py]) {
                    p[py] = px;
                } else if (r[px] < r[py]) {
                    p[px] = py;
                } else {
                    p[py] = px;
                    r[px] += 1;
                }
            }
        }
    }
}
```

## 218. The Skyline Problem
https://leetcode.com/problems/the-skyline-problem/

找建筑物发生高度变化边及其高度.
1. 使用 set 找出所有边的 index, 并对所有边进行从左到右排序
2. 离散化建表, 用于记录每条边 原index 所对应的 0-index, 用于在 第 4 步和 新高度对齐
3. 将建筑物按照高度排序, 从高到低
4. 创建新的高度 array
5. 遍历每个建筑, 找到其左右边, 更新其中间的高度, 使用unionFind实现快速跳跃, 从左边界找到有效的右边界, 有效右边界: 

注意: 绝对不能使用 按秩排序, 因为这里的 UnionFind 我们需要严格的前后(从左到右)关系, 即从 左边, 在 O(1) 内, 找到其联通的右边. 

```java
class Solution {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        // get all edges
        Set<Integer> edgeSet = new HashSet<>();
        for (int[] b : buildings) {
            edgeSet.add(b[0]);
            edgeSet.add(b[1]);
        }
    
        // edges in order
        Integer[] edges = edgeSet.toArray(new Integer[edgeSet.size()]);
        Arrays.sort(edges);
        
        Map<Integer, Integer> edgeMap = new HashMap<>();
        for (int i = 0; i < edges.length; i++) {
            edgeMap.put(edges[i], i);
        }
        
        Arrays.sort(buildings, (o1, o2) -> o2[2] - o1[2]);  // 高度, 从高到低
        
        int n = edges.length;
        UnionFind uf = new UnionFind(n);
        int[] heights = new int[n];
        
        for (int[] b : buildings) {
            int leftEdge = b[0], rightEdge = b[1], h = b[2];
            int leftIndex = edgeMap.get(leftEdge), rightIndex = edgeMap.get(rightEdge);
            // 不使用 uf 的话, 原操作
            // for (int i = leftIndex; i <= rightIndex; i++) {
            //     height[i] = h;
            // }
            while (leftIndex < rightIndex) {        // 把所有在左右中间的, 连通起来
                leftIndex = uf.find(leftIndex);
                if (leftIndex < rightIndex) {
                    uf.union(leftIndex, rightIndex);
                    heights[leftIndex] = h;
                    leftIndex++;
                }
            }
        }
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (i == 0 || heights[i] != heights[i-1])
                res.add(List.of(edges[i], heights[i]));
        }   
        return res;
    }
    
    class UnionFind {
        int[] p;
        int[] r;
        
        public UnionFind(int n) {
            p = new int[n];
            r = new int[n];
            for (int i = 0; i < n; i++) {
                p[i] = i;
                r[i] = 0;
            }
        }
        
        public int find(int x) {
            if (p[x] == x)
                return x;
            return p[x] = find(p[x]);
        }

        public void union(int x, int y) {
            p[x] = p[y];
        }
    }
}
```