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

Number of Provinces: https://leetcode.com/problems/number-of-provinces/

经典例题, 直接连接使用 UF 即可