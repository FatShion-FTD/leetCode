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