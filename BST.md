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