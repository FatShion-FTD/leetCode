# BinarySearchTree BST
### 二叉树的一种, 特性: left.val < node.val && right.val > node.val

# Problems
## Traverse 总结
### 不管对于 iteration 还是 recursion 实现, 都要意识到一点: 那就是在探索完毕后, 其实还有一个返回的过程, 我们对于 node 的访问, 除了 preOrder, 都应在返回的路上完成.对于 iteration, 返回过程是在 node == null 时; 对于recursion, 返回在每次递归调用之后.

144. Binary Tree Preorder Traversal: https://leetcode.com/problems/binary-tree-preorder-traversal/

前序访问, 迭代DFS实现: 左 中 右, 在所有左探的路上记录 val
```java
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    if(root == null)    return res;
    Deque<TreeNode> stack = new ArrayDeque<>();
    while(root != null || !stack.isEmpty()){
        if(root != null){
            stack.push(root);
            res.add(root.val);  // 左探路上记录
            root = root.left;   // 左
        }else{
            TreeNode node = stack.pop();    // 中
            root = node.right;   // 右
        }
    }
    return res;
}
```

94. Binary Tree Inorder Traversal: https://leetcode.com/problems/binary-tree-inorder-traversal/

中序访问, 迭代DFS实现: 中 左 右, 则在左探结束后, 把值加入
```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> res = new ArrayList<>();
    Deque<TreeNode> stack = new ArrayDeque<>();
    while(root != null || !stack.isEmpty()){
        if(root != null){
            stack.push(root);
            root = root.left;   // 左
        }else{ 
            TreeNode node = stack.pop();    // 中
            res.add(root.val);
            root = node.right;   // 右
        }
    }
    return res;
}
```

145. Binary Tree Postorder Traversal: https://leetcode.com/problems/binary-tree-postorder-traversal/

后序访问, 迭代DFS实现: 左 右 中, 如果使用 右探, 则走 中 右 左, 此时需要 反转!
```java
public List<Integer> postorderTraversal(TreeNode root) {
   LinkedList<Integer> res = new LinkedList<>();
   Deque<TreeNode> stack = new ArrayDeque<>();
   while(root != null || !stack.isEmpty()){
       if(root != null){
           stack.push(root);
           res.addFirst(root.val);
           root = root.right;   // 右
       }else{
           TreeNode node = stack.pop();    // 中
           root = node.left;   // 左
       }
   }
   return res;
}
```

三种访问的递归实现: 
```java
private void traversalHelper(TreeNode root){
    if(root == null) return;
    res.add(root.val);      // preOrder 实现
    traversalHelper(root.left);
    res.add(root.val);      // inOrder 实现
    traversalHelper(root.right);
    res.add(root.val);      // postOrder 实现
}
```

538. Convert BST to Greater Tree: https://leetcode.com/problems/convert-bst-to-greater-tree/

使用 postOrder 进行访问, 并记录suffix, 返回的路上 node.val += suffix 即可

```java
public TreeNode convertBST(TreeNode root) {
   Deque<TreeNode> stack = new ArrayDeque<>();
   TreeNode node = root;
   int suffix = 0;
   while(!stack.isEmpty() || node != null){
       if(node != null){
           stack.push(node);
           node = node.right;
       }else{
           TreeNode mid = stack.pop(); // 中间
           int temp = mid.val;
           mid.val += suffix;
           suffix += temp;
           node = mid.left;
       }
   }
   return root;
}
```


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