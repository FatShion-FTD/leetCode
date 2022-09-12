# 分治 Divide and conquer
递归的一种, 将问题划分为两个或者多个小的子问题, 递归去解决子问题直到 base case. 可以认为是一种 bottom-up DP. 分治也是 merge sort, quick sort的母体.

常见的模板是将问题左右划分. 然后对相同结构的 左右子问题, 进行递归处理. 注意问题的base case即可.

```java
public int solve(int start, int end){
    if (start > end)                            // base
        return 0;
    int divideIndex = divide(start, end);       // find divider
    int left = solve(start, divideIndex - 1);   // get left
    int right = solve(divideIndex + 1, end);    // get right
    int res = process();                        // process sth.
    return res;                                 // return res
}
```



## 95. Unique Binary Search Trees II
https://leetcode.com/problems/unique-binary-search-trees-ii/

分治生成左右子树, 并在递归函数中拼接即可. G[n] = F[1, i - 1] * F[i + 1, n], 根为 i, F[i, j]表示以 i 为根, 以 j 为结尾的子树. 

```java
class Solution {
    public List<TreeNode> generateTrees(int n) {
        if (n == 0)
            return new ArrayList<>();
        
        return generateTrees(1, n);
    }
    
    public List<TreeNode> generateTrees(int root, int n) {
        List<TreeNode> res = new ArrayList<>();
        if (root > n) {     // base
            res.add(null);
            return res;
        }
        
        for (int i = root; i <= n; i++) {
            List<TreeNode> leftSubs = generateTrees(root, i - 1);
            List<TreeNode> rightSubs = generateTrees(i + 1, n);
            for (TreeNode left : leftSubs) {
                for (TreeNode right : rightSubs) {
                    TreeNode node = new TreeNode(i);            // must allocate new address
                    node.left = left;
                    node.right = right;
                    res.add(node);
                }
            } 
        }
        return res;
    }
}
```