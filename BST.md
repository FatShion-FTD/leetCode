# BinarySearchTree BST
### 二叉树的一种, 特性: left.val < node.val && right.val > node.val

## Problems
700. Search in a Binary Search Tree: https://leetcode.com/problems/search-in-a-binary-search-tree/

BST 的搜索, 递归过于简单, 则使用 stack 进行 iteration

```java
public TreeNode searchBST(TreeNode root, int val) {
    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push(root);
    
    while(!stack.isEmpty() && root != null){
        TreeNode curr = stack.pop();
        if(curr.val == val) return curr;
        if(curr.val > val && curr.left != null){
            stack.push(curr.left);
        }else if(curr.right != null){
            stack.push(curr.right);
        }
    }
    
    return null;
}
```
669. Trim a Binary Search Tree: https://leetcode.com/problems/trim-a-binary-search-tree/

标准递归, 要修枝时: 直接将 root 作为返回的节点, 然后递归搜索有效范围

```java
public TreeNode trimBST(TreeNode root, int low, int high) {
   if(root == null) return null;
   if(root.val > high){
       root = trimBST(root.left, low, high);
   }else if( root.val < low){
       root = trimBST(root.right, low, high);
   }else{
       root.left = trimBST(root.left, low, high);
       root.right = trimBST(root.right, low, high);
   }
   return root;
}
```

迭代实现: 先找有效 root, 再去左右分别修枝, 在左边一直找最大的, 右边一直找最小的

```java
public TreeNode trimBST(TreeNode root, int low, int high) {
   if(root == null)    return null;
   while(root != null && (root.val < low || root.val > high)){
       if(root.val < low){
           root = root.right;
       }else if(root.val > high){
           root = root.left;
       }
   }
   TreeNode dummy = root;
   // trim left tree where all val < low
   while(dummy != null){
       while(dummy.left != null && dummy.left.val < low){
           dummy.left = dummy.left.right;  // 找最大方向
       }
       dummy = dummy.left;
   }
   dummy = root;
   // trim right tree where all val > high
   while(dummy != null){
       while(dummy.right != null && dummy.right.val > high){
           dummy.right = dummy.right.left; // 找最小方向
       }
       dummy = dummy.right;
   }
   return root;
}
```