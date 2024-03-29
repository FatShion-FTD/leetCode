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

## 698. Partition to K Equal Sum Subsets
https://leetcode.com/problems/partition-to-k-equal-sum-subsets/    
同款题目, 473. Matchsticks to Square     
https://leetcode.com/problems/matchsticks-to-square/

DFS直接应用即可, 但是使用DFS在回溯时候, 有2个 trick 加速, 不然会TLE:
1. 针对越界情况, 如果从 较大数字 进行反向遍历, 则可以快速退出
2. sort 后跳过相同元素 进行剪枝, 和 subset 2 类似

```java
 public boolean canPartitionKSubsets(int[] nums, int k) {
     int sum = 0;
     for(int stick : nums){
         sum += stick;
     }
     if(sum % k != 0)    return false;
     Arrays.sort(nums);
     
     return dfs(nums, new int[k], nums.length - 1, sum / k);
 }
 
 private boolean dfs(int[] nums, int[] lens, int index, int target){
     if(index == -1)
         return true;
     
     int stick = nums[index];
     for(int j = 0; j < lens.length; j++){
         if(lens[j] + stick > target || (j > 0 && lens[j] == lens[j-1])) 
             continue;
         lens[j] += stick;
         if(dfs(nums, lens, index - 1, target))   
             return true;
         lens[j] -= stick;
     }
     return false;
 }
```
## 576. Out of Boundary Paths
https://leetcode.com/problems/out-of-boundary-paths/

3维记忆 + DFS. 两点: 
1. 3维记忆, row index, col index 和 剩余move
2. 注意记忆初始化, 一定要初始化为 -1, 因为初始化为0, 有些edge case计算后的值就是0, 会出现重复计算

```java
 static int MOD = 1000000007;

 public int findPaths(int m, int n, int max, int row, int col) {
     int[][][] dp = new int[m][n][max + 1];
     for(int[][] arr1 : dp)
         for(int[] arr : arr1)
             Arrays.fill(arr, -1);
     
     return dfs(dp, m, n, max, row, col);
 }
 
 private int dfs(int[][][] dp, int m, int n, int max, int row, int col){
     if(max < 0)
         return 0;
     if(row < 0 || row == m || col < 0 || col == n)
         return 1;
     if(dp[row][col][max] != -1)
         return dp[row][col][max];
     
     int res = 0;
     res = (res + dfs(dp, m, n, max - 1, row + 1, col)) % MOD;
     res = (res + dfs(dp, m, n, max - 1, row - 1, col)) % MOD;
     res = (res + dfs(dp, m, n, max - 1, row, col + 1)) % MOD;
     res = (res + dfs(dp, m, n, max - 1, row, col - 1)) % MOD;
     dp[row][col][max] = res;                 
     return res;         
 }
```

2359. Find Closest Node to Given Two Nodes
https://leetcode.com/problems/find-closest-node-to-given-two-nodes/

用个 MAP 存 node1 经过的节点 + 距离, 从 node2 开始走, 如果 node2 经过的结点在 map 中, 则要求 二者之中较远距离 最小. (而不是TMD, node1 到 node 距离最远, node2 到 node 距离最近, CNM)

```java
 public int closestMeetingNode(int[] edges, int node1, int node2) {
     // map store the distance from node1, and from node2, trying to find the node
     // which is in the map, with the res = min(max(dis1, dis2), res)
     
     Map<Integer, Integer> map1 = new HashMap<>();
     int node = node1, dis = 0;
     while (!map1.containsKey(node) && node != -1) {
         map1.put(node, dis++);
         node = edges[node];
     }
     
     node = node2; dis = 0;
     Map<Integer, Integer> map2 = new HashMap<>();
     int res = Integer.MAX_VALUE, max = Integer.MAX_VALUE;
     while (!map2.containsKey(node) && node != -1) {
         if (map1.containsKey(node)) {
             int curr = Math.max(dis, map1.get(node));        // get the max dist to node from node1 and node2 
             if (curr <= max) {
                 res = curr == max ? Math.min(res, node) : node;
                 max = curr;
             }
         }
         map2.put(node, dis++);
         node = edges[node];
     }
     
     return res == Integer.MAX_VALUE ? -1 : res;
 }
```

## 417. Pacific Atlantic Water Flow
https://leetcode.com/problems/pacific-atlantic-water-flow/

DFS + **反向思维**, 使用 **DFS 从 Pac 和 Atl 的边缘, 找到所有可以反向流入的点**, 再构建答案

为什么不是正向思维, 因为使用正向思维, 从 一个点 开始, 途径的点会有重复计算, 而且这个重复的情况并不能 很好的记录下来, 需要已经计算的 点 的四向信息; 一个点可能有多条流入途径; 判断条件不好写, 单纯的 && 会误判, 需要额外的DFS func. 寄

```java
final static int[] DIRS = new int[]{-1, 0, 1, 0, -1};
List<List<Integer>> res;
boolean[][] isPac, isAtl;

public List<List<Integer>> pacificAtlantic(int[][] heights) {
  // rain, if neighb's height <= curr height, 
  // from the edge node, find all reachable node in Island from both Ocean
  // build res based on 2 memo
  // dfs, with memo
  // 2 memo, isPac, isAtl
  res = new ArrayList<>();
  
  if (heights == null || heights.length == 0)
      return res;
  
  int n = heights.length, m = heights[0].length;
  isPac = new boolean[n][m];
  isAtl = new boolean[n][m];
  
  // search from bondary, find all reachable nodes
  for (int i = 0; i < n; i++) {
      dfs(heights, isPac, i, 0);
      dfs(heights, isAtl, i, m - 1);
  }
  
  for (int j = 0; j < m; j++) {
      dfs(heights, isPac, 0, j);
      dfs(heights, isAtl, n - 1, j);
  }
  
  for (int i = 0; i < n; i++) {
      for (int j = 0; j < m; j++) {
          if (isPac[i][j] && isAtl[i][j])
              res.add(Arrays.asList(i, j));
      }   
  }
  
  return res;
}

private void dfs (int[][] h, boolean[][] v, int r, int c) {
  v[r][c] = true;
  
  for (int i = 1; i < DIRS.length; i++) {
      int nr = r + DIRS[i-1], nc = c + DIRS[i];
      
      if (nr < 0 || nr >= h.length || nc < 0 || nc >= h[0].length || v[nr][nc])
          continue;
      
      if (h[nr][nc] >= h[r][c])
          dfs(h, v , nr, nc);
  }
}
```

## 386. Lexicographical Numbers
https://leetcode.com/problems/lexicographical-numbers/

DFS, 也可以是O(n), 这种类似 Trie 树的 DFS, 遍历效果也是 O(n)    
从 1-9 作为 base 开始, DFS 构建新数字即可, curr * 10 + i

```java
List<Integer> res = new ArrayList<>();

public List<Integer> lexicalOrder(int n) {
  if (n == 0)
      return res;
  
  for (int i = 1; i < 10; i++) {
      dfs(n, i);
  }
  return res;
}

private void dfs (int n, int curr) {
  if (curr > n)
      return;
  
  res.add(curr);
  for (int i = 0; i < 10; i++) {
      dfs(n, 10 * curr + i);
  }
}
```

## 388. Longest Absolute File Path
https://leetcode.com/problems/longest-absolute-file-path/

DFS + String, 把这个结构看成树, \t 储存了层数的信息, 注意 stack 只存当前层之前的信息, 一旦 stack 大小 > 当前层, 说明深度不对, 需要pop; 以及使用 replace 替换来计算长度

```java
public int lengthLongestPath(String str) {
  // DFS
  // \n means goto subdir/file, \t means the depth of next sub
  // using stack to store the length of dir/file
  if (str == null || str.length() == 0)
      return 0;
  
  int i = 0, n = str.length();
  int res = 0, curr = 0;
  String[] strs = str.split("\n");
  Deque<Integer> stack = new ArrayDeque<>();
  
  for (String s : strs) {
      int level = countLevel(s);
      
      while (stack.size() > level) {      // stack is not at same level as curr dir/file
          curr -= stack.pop();
      }
      
      int len = s.replaceAll("\t", "").length() + 1;      // +1: remain one '/' at last
      curr += len;
      
      if (s.contains(".")) {
          res = Math.max(res, curr - 1);
      }
      stack.push(len);
  }
  
  
  return res;
}

private int countLevel (String s) {
  return s.length() - s.replace("\t", "").length();
}
```

## 2397. Maximum Rows Covered by Columns
https://leetcode.com/problems/maximum-rows-covered-by-columns/

DFS 查询所有可能 col index 组合, 然后在结尾状态验证: 当 m[row][col] = 1 时, 其 col 必须在 set 中. 如果为 0 则无所谓啦

```java
int res = 0;
public int maximumRows(int[][] matrix, int numSelect) {
  // 1. row only has 0, 2. m[row][col] = 1, col has to be in s. -> m[row][col] == 1, col has to be in s.
  dfs(matrix, numSelect, 0, new HashSet<>());
  return res;
}

private void dfs(int[][] m, int ret, int index, Set<Integer> v) {
  if (ret == 0 || index == m[0].length) {
      int covered = 0;
      for (int i = 0; i < m.length; i++) {
          boolean isCover = true;
          for (int j = 0; j < m[0].length; j++) {
              if (m[i][j] == 1 && !v.contains(j)) {
                  isCover = false;
                  break;
              }
          }
          if (isCover) covered ++;
      }
      res = Math.max(res, covered);
      return;
  }
  // pick curr col
  v.add(index);
  dfs(m, ret - 1, index + 1, v);
  v.remove(index);
  
  // not pick curr col
  dfs(m, ret, index + 1, v);
  return;
}
```