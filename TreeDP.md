# Tree DP
树形DP 是使用树的结构实现DP的一种算法, 主要思想是bottom up的DP; 使用 DFS 进行TreeNode 的遍历访问

分为主要两种: 
1. 节点选择: $f[i][0]=f[j][1]$ or $f[i][1] = max / min (f[j][1], f[j][0])$ where j is children of i node  
key: 树形数据, 相邻不能同时取, 求最大最小, logN   
经典题: 没有上司的舞会, 打家劫舍3

2. 树形背包: $f[v][k]=f[u][k]+val$ or $f[u][k] = max (f[u][k], f[v][k-1])$ 带有先决条件的背包, 如 先修课





## 337. House Robber III
https://leetcode.com/problems/house-robber-iii/

两种状态: 偷 和 不偷

```java
class Solution {
    public int rob(TreeNode root) {
        // res[0] = not steal, res[1] = steal
        int[] res = dfs(root);
        return Math.max(res[0], res[1]);
    }
    
    private int[] dfs(TreeNode node){
        if(node == null)    return new int[]{0, 0};
        int[] left = dfs(node.left);
        int[] right = dfs(node.right);
        int[] res = new int[2];
        res[0] = Math.max(left[0], left[1]) + Math.max(right[0], right[1]); // not steal cur, find max anyway
        res[1] = node.val + left[0] + right[0]; // steal cur, not steal child
        return res;
    }
}
```

## 968. Binary Tree Cameras
https://leetcode.com/problems/binary-tree-cameras/

树形DP, 三种状态转移: NOT covered, covered by CHILDREN, covered by ITSELF 转移即可

```java
class Solution {
    public int minCameraCover(TreeNode root) {
        // Tree DP, like state machine
        // dp[0] = min camera that not covered, 
        // dp[1] = min camera that merely covered, no camera.
        // dp[2] = min camert that node is covered with a camera on
        int[] res = dfs(root);
        return Math.min(res[1], res[2]);
    }
    
    private int[] dfs(TreeNode node){
        if(node == null)    return new int[]{0, 0, 1001};
        int[] left = dfs(node.left);
        int[] right = dfs(node.right);
        int[] res = new int[3];
        // left and right is covered, so root is not covered
        res[0] = left[1] + right[1];    
        // root is covered by left or right camera, min left camera on and min right covered, vice versa
        res[1] = Math.min(left[2] + Math.min(right[1], right[2]), right[2] + Math.min(left[1], left[2]));
        // root is set a camera, no matter how left and right is, just find min
        res[2] = 1 + Math.min(left[0], Math.min(left[1], left[2])) + 
            Math.min(right[0], Math.min(right[1], right[2]));
        return res;
    }
}
```
## 124. Binary Tree Maximum Path Sum
https://leetcode.com/problems/binary-tree-maximum-path-sum/    

1. path sum = node.val + left + right
2. max partial path sum = node.val + Math.max(left, right)
3. Always check whether node.val is greater than any above sum.

```java
 int res = Integer.MIN_VALUE;
 public int maxPathSum(TreeNode root) {
     // path = node.val + left + right
     // goto upper node.val + Math.max(left, right)
     dfs(root);
     return res;
 }
 
 private int dfs(TreeNode root){
     if(root == null)    return 0;
     int left = dfs(root.left);
     int right = dfs(root.right);
     
     int pathSum = root.val + Math.max(left, 0) + Math.max(right, 0);    // ignore subtree if < 0
     int upperSum = root.val + Math.max(left, right);    // if goto parent node, use it
     
     pathSum = Math.max(pathSum, root.val);      // find max path
     res = Math.max(res, pathSum);
     
     return Math.max(upperSum, root.val);   
 }
```