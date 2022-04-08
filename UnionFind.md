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