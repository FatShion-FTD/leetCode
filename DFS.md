# DFS
## DFS 的两个核心: 递归和回溯

通过 recursion 的办法访问元素直到 base case , 通过 backtrack 的办法离开递归构建答案

## 典中典例题   Permutations
https://leetcode.com/problems/permutations/

使用 visited 记录访问过的元素的标准backtrack

```java
List<List<Integer>> res;
List<Integer> temp;
boolean[] visited;

public List<List<Integer>> permute(int[] nums) {
    res = new ArrayList<>();
    temp = new ArrayList<>();
    visited = new boolean[nums.length];
    dfs(nums, 0);
    return res;
}

private void dfs(int[] nums, int depth){
    if(depth == nums.length){
        res.add(new ArrayList<>(temp));
    }
    for(int i = 0; i < nums.length; i++){
        if(!visited[i]){
            temp.add(nums[i]);
            visited[i] = true;
            dfs(nums, depth+1);
            temp.remove(temp.size()-1);
            visited[i] = false;
        }
    }
}
```


## 399. Evaluate Division: https://leetcode.com/problems/evaluate-division/

先使用edges, 构建双向有向图, 再通过dfs构建和UnionFind类似的 root 和 val 对应表.

1. 使用 edges = Map<Node, Map<Parent, Val>> 存双向图信息
2. 使用 seen = Set() 避免节点的重复访问
3. root 和 vals 记录每个节点对应 跟 和 到跟的值


对于这些类似的图问题, 常使用dfs + visited 进行遍历访问
```java
HashSet<String> seen = new HashSet<>();
HashMap<String, String> root = new HashMap<>();
HashMap<String, Double> vals = new HashMap<>();
HashMap<String, HashMap<String, Double>> edges = new HashMap<>();
public double[] calcEquation(String[][] equations, double[] values, String[][] queries) {
   int n = equations.length, m = queries.length;
   for (int i = 0; i < n; ++i) {
       String x = equations[i][0], y = equations[i][1];
       if (!edges.containsKey(x))
           edges.put(x, new HashMap<String, Double>());
       if (!edges.containsKey(y) )
           edges.put(y, new HashMap<String, Double>());
       edges.get(x).put(y, values[i]);
       edges.get(y).put(x, 1 / values[i]);
   }
   for (String x : edges.keySet()) {
       if (!seen.contains(x)) dfs(x, x, 1);
   }
   double[] res = new double[m];
   for (int i = 0; i < m; ++i) {
       String x = queries[i][0], y = queries[i][1];
       String px = root.getOrDefault(x, x), py = root.getOrDefault(y, y);
       res[i] = px == py ? vals.get(x) / vals.get(y) : -1.0;
   }
   return res;
}

public void dfs(String x, String p, double v) {
   vals.put(x, v);
   root.put(x, p);
   seen.add(x);
   for (String y : edges.get(x).keySet()) {
       if (!seen.contains(y))
           dfs(y, p, v * edges.get(y).get(x));
   }
}
```

## 980. Unique Paths III
https://leetcode.com/problems/unique-paths-iii/

非常直接的 暴力DFS 解决hard问题典型

```java    
 int res = 0;
 int sx = -1, sy = -1, valid = 0;
 
 public int uniquePathsIII(int[][] grid) {
     for(int i = 0; i < grid.length; i++){
         for(int j = 0; j < grid[0].length; j++){
             if(grid[i][j] == 0) valid += 1;
             else if(grid[i][j] == 1){
                 sx = i; sy = j; valid += 1;
             }
         }
     }
     dfs(grid, sx, sy);
     return res;
 }
 
 private void dfs(int[][] grid, int x, int y){
     // -1 or -2 no way
     if(x < 0 || x >= grid.length || y < 0 || y >= grid[0].length || grid[x][y] < 0)
         return;
     // end
     if(grid[x][y] == 2){
         if(valid == 0)  res+=1;
         return;
     }
     grid[x][y] = -2;
     valid -= 1;
     dfs(grid, x+1, y);
     dfs(grid, x-1, y);
     dfs(grid, x, y+1);
     dfs(grid, x, y-1);
     grid[x][y] = 0;
     valid += 1;
 }
```

## 97. Interleaving String
https://leetcode.com/problems/interleaving-string/

![https://assets.leetcode.com/uploads/2020/09/02/interleave.jpg](https://assets.leetcode.com/uploads/2020/09/02/interleave.jpg)

把两个 String 按照 row 和 col 排列, 就形成了一个 2D Array, 在这里找到 valid String == s3 即可

```java
 public boolean isInterleave(String s1, String s2, String s3) {
     int n = s1.length(), m = s2.length(), l = s3.length();
     if(n + m != l)  return false;
     
     return dfs(s1, s2, s3, 0, 0, 0, new boolean[n+1][m+1]);
 }
 
 private boolean dfs(String s1, String s2, String s3, int row, int col, int len, boolean[][] visited){
     if(visited[row][col])   return false;
     if(len == s3.length())  return true;
     boolean res = 
         row < s1.length() && s1.charAt(row) == s3.charAt(len) && dfs(s1, s2, s3, row + 1, col, len + 1, visited) ||
         col < s2.length() && s2.charAt(col) == s3.charAt(len) && dfs(s1, s2, s3, row, col + 1, len + 1, visited) ;
     visited[row][col] = true;
     return res;
 }
```

## 93. Restore IP Addresses
https://leetcode.com/problems/restore-ip-addresses/

B事儿比较多的 backtracking, IP 验证三个点:
1. num 不能大于255
2. 刚好4个
3. 可以 0, 但是不能 leading zero

```java
 List<String> res;
 public List<String> restoreIpAddresses(String s) {
     res = new ArrayList<>();
     dfs(s, 0, new ArrayList<>());
     return res;
 }
 
 private void dfs(String s, int index, List<String> t){
     if(index > s.length() || t.size() > 4)  return;
     if(index == s.length() && t.size() == 4){
         StringBuilder sb = new StringBuilder();
         for(String str : t) sb.append(str + '.');
         sb.deleteCharAt(sb.length() - 1);
         res.add(sb.toString());
         return;
     }
     for(int i = 0; i < 3 && i + index < s.length(); i++){
         String sub = s.substring(index, index + i + 1);
         int num = Integer.parseInt(sub);
         if(i > 0 && num / 10 == 0) return;
         if(num > 255)   return;
         t.add(sub);
         dfs(s, i + index + 1, t);
         t.remove(t.size() - 1);
     }
 }
```

## 90. Subsets II
https://leetcode.com/problems/subsets-ii/

子集2, 核心: 排序后去重, but for duplicates, (1,1) -> ONE 1, TWO 1, but NOT 2 ONE 1. Which means always carry one if duplicated, after that, AVOID add it again

```java
 List<List<Integer>> res;
 public List<List<Integer>> subsetsWithDup(int[] nums) {
     res = new ArrayList<>();
     Arrays.sort(nums);
     dfs(new ArrayList<>(), 0, nums);
     return res;
 }
 
 private void dfs(List<Integer> t, int index, int[] nums){
     if(index == nums.length + 1)    return;
     res.add(new ArrayList<>(t));
     for(int i = index; i < nums.length; i++){
         if(i != index && nums[i] == nums[i-1])  continue;   // always carry one
         t.add(nums[i]);
         dfs(t, i + 1, nums);
         t.remove(t.size() - 1);
     }
 }
```

# Path in Grid 系列
## 329. Longest Increasing Path in a Matrix
https://leetcode.com/problems/longest-increasing-path-in-a-matrix/

在 matrix 中找 longest path, 方法 DFS + memo. 核心在于 memo 的记忆内容:    
存到当前节点为止的 max length path.    
转移时使用: Math.max(memo[i][j], 1 + dfs());

```java
 int res = 0;
 int[][] memo;
 
 public int longestIncreasingPath(int[][] matrix) {
     int n = matrix.length, m = matrix[0].length;
     memo = new int[n][m];
     for(int[] mem : memo){
         Arrays.fill(mem, -1);
     }
     
     for(int i = 0; i < n; i++){
         for(int j = 0; j < m; j++){
             res = Math.max(res, dfs(matrix, i, j));
         }
     }
     
     return res;
 }
 
 private int dfs(int[][] m, int i, int j){
     if(memo[i][j] != -1){
         return  memo[i][j];
     }
     memo[i][j] = 1;
     if(i - 1 >= 0 && m[i][j] < m[i-1][j]){
         memo[i][j] = Math.max(memo[i][j], 1 + dfs(m, i-1, j));
     }
     if(j - 1 >= 0 && m[i][j] < m[i][j-1]){
         memo[i][j] = Math.max(memo[i][j], 1 + dfs(m, i, j-1));
     }
     if(i + 1 < m.length && m[i][j] < m[i+1][j]){
         memo[i][j] = Math.max(memo[i][j], 1 + dfs(m, i+1, j));
     }
     if(j + 1 < m[0].length && m[i][j] < m[i][j+1]){
         memo[i][j] = Math.max(memo[i][j], 1 + dfs(m, i, j+1));
     }
     return memo[i][j];
 }
```

## 2328. Number of Increasing Paths in a Grid
https://leetcode.com/problems/number-of-increasing-paths-in-a-grid/

方法同上, memo + DFS, 但是 memo 记录所有 valid path 的总数

```java
 final static int MOD = 1000000007;
 int res = 0;
 int[][] memo;
 
 public int countPaths(int[][] grid) {
     int m = grid.length, n = grid[0].length;
     if(m == 0 || grid == null)  return res;
     
     memo = new int[m][n];
     for(int[] mem : memo){
         Arrays.fill(mem, -1);
     }
     
     for(int i = 0; i < m; i++){
         for(int j = 0; j < n; j++){
             res = (res + dfs(grid, i, j)) % MOD;
         }
     }
     return res;
 }
 
 private int dfs(int[][] grid, int i, int j){
     if(memo[i][j] != -1){
         return memo[i][j];
     }
     memo[i][j] = 1;
     
     if(i - 1 >= 0 && grid[i][j] < grid[i - 1][j]) 
         memo[i][j] = (memo[i][j] + dfs(grid, i - 1, j)) % MOD;
     
     if(j - 1 >= 0 && grid[i][j] < grid[i][j - 1]) 
         memo[i][j] = (memo[i][j] + dfs(grid, i, j - 1)) % MOD;
     
     if(i + 1 < grid.length && grid[i][j] < grid[i + 1][j]) 
         memo[i][j] = (memo[i][j] + dfs(grid, i + 1, j)) % MOD;
     
     if(j + 1 < grid[0].length && grid[i][j] < grid[i][j + 1]) 
         memo[i][j] = (memo[i][j] + dfs(grid, i, j + 1)) % MOD;
     
     return memo[i][j];
 }
```