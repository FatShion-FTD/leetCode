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


399. Evaluate Division: https://leetcode.com/problems/evaluate-division/

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