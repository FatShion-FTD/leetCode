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

超高级实现 Morris, 不使用 stack 和 recursion, O(1) space
![https://images0.cnblogs.com/blog/300640/201306/14214057-7cc645706e7741e3b5ed62b320000354.jpg](https://images0.cnblogs.com/blog/300640/201306/14214057-7cc645706e7741e3b5ed62b320000354.jpg)
```java
void MorrisTraversal(Node root)
{
   Node current, pre;
   if (root == null)    return;
   current = root;
   while (current != null) {
       if (current.left == null) {  // current 此时为最小值(左下), 中
           System.out.print(current.data + " ");    
           current = current.right; // current 走 右子树
       } else {
           pre = current.left;  // 找值仅次于 current.val 的前一 node 
           while (pre.right != null && pre.right != current)
               pre = pre.right;
       // 前一节点的右节点 为空, 则把 前一节点 和 当前节点 构建返回的链接, 图中虚线
           if (pre.right == null) {
               pre.right = current;
               current = current.left;
           } else { 
     // 前一节点的右节点 就是 当前节点, 说明抵达 当前子树最右 brach, 向右下遍历
               pre.right = null;
               System.out.print(current.data + " ");
               current = current.right;
           } /* End of if condition pre->right == NULL*/
       } /* End of if condition current->left == NULL*/
   } /* End of while */
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
897. Increasing Order Search Tree: https://leetcode.com/problems/increasing-order-search-tree/

两种解法: iteration 实现: 使用 stack 进行 inorder traverse + Dummmy Head 重构. 切记不可直接使用使用in-place, 不然会出现提前剪枝

相对来说, 这道题的iteration更加好想

```java
public TreeNode increasingBST(TreeNode root) {
   if(root == null)    return null;
   TreeNode newHead = new TreeNode();
   TreeNode prev = newHead;
   Deque<TreeNode> stack = new ArrayDeque<>();
   while(!stack.isEmpty() || root != null){
       if(root != null){
           stack.push(root);
           root = root.left;
       }else{
           root = stack.pop();
           prev.right = root;
           prev = prev.right;
           root.left = null;
           root = root.right;
       }
   }
   return newHead.right;
}
```
递归实现: 传入两个参数, 当前 node 和 inorder 下的 next node. 在node本身为空时, 抵达base, 返回 next(其 parent); 访问 node.left 时, 递归的 next 是当前 node; left 置空; 对于比自己大的 node.right, 放入 right, 递归的next 还是 next;

```java
public TreeNode increasingBST(TreeNode root) {
   return  increasingBST(root, null);
}

public TreeNode increasingBST(TreeNode root, TreeNode next){
   if(root == null)    return next;
   TreeNode res = increasingBST(root.left, root);
   root.left = null;
   root.right = increasingBST(root.right, next);
   return res;
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

230. Kth Smallest Element in a BST: https://leetcode.com/problems/kth-smallest-element-in-a-bst/

标准 in-order, 查找第N个

```java
public int kthSmallest(TreeNode root, int k) {
   Deque<TreeNode> stack = new ArrayDeque<>();
   while(!stack.isEmpty() || root != null){
       if(root != null){
           stack.push(root);
           root = root.left;
       }else{
           root = stack.pop();
           if(k-- == 1)    return root.val;
           root = root.right;
       }
   }
   return -1;
}
```

使用 Binary Search的办法: 记录 左子树的node数量, 如果 左子树数量 == k 则返回; 如果左子树数量 > k, 则进入左子树; 如果 < k, 则进入 右子树, k = k - 左子树数量 - 1 (当前node)
```java
public int kthSmallest(TreeNode root, int k) {
   int mid = countNode(root.left);
   if( k <= mid){
       return kthSmallest(root.left, k);
   }else if(k > mid + 1){
       return kthSmallest(root.right, k-1-mid);
   }
   return root.val;
}

private int countNode(TreeNode node){
   if(node == null)    return 0;
   return 1 + countNode(node.left) + countNode(node.right);
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
99. Recover Binary Search Tree: https://leetcode.com/problems/recover-binary-search-tree/

交换由两种情况: 
1. 邻接, 1,2,4,3,5, 这种情况下直接 3 和 4 左右互换即可;
2. 非邻接, 1,2,5,4,3, 这种情况下, 需要记录 第一组 pair 5 和 4, 以及第二组 pair 4 和 3, 交换 5 和 3;

使用 list 保存这些错误pair, 如果 list length == 1, 则说明邻接, 互换; 如果非邻接, 则 让 last pair 的 small value 和 first pair 的 large value 互换


```java
public void recoverTree(TreeNode root) {
   Deque<TreeNode> stack = new ArrayDeque<>();
   TreeNode prev = new TreeNode(Integer.MIN_VALUE);
   List<TreeNode[]> list = new ArrayList<>();
   while(root!=null || !stack.isEmpty()){
       if(root != null){
           stack.push(root);
           root = root.left;
       }else{
           root = stack.pop();
           if(root.val < prev.val){
               list.add(new TreeNode[]{prev, root});
           }
           prev = root;
           root = root.right;
       }
   }
   if(list.size() == 1){
       int temp = list.get(0)[0].val;   // largest value in pairs
       list.get(0)[0].val = list.get(0)[1].val;
       list.get(0)[1].val = temp;
   }else{
       int temp = list.get(0)[0].val;
       list.get(0)[0].val = list.get(1)[1].val; // smallest
       list.get(1)[1].val = temp;
   }
   return;
}
```
105. Construct Binary Tree from Preorder and Inorder Traversal: https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

构造二叉树, 3点: 
1. 使用 递归 办法找根节点

```java
 public TreeNode buildTree(int[] preorder, int[] inorder) {
     return buildTree(0,inorder.length - 1,0, preorder, inorder);
 }
 
 private TreeNode buildTree(int inStart, int inEnd, int preStart, int[] preorder, int[] inorder){
     if(preStart > preorder.length - 1 || inStart > inEnd){
         return null;
     }
     TreeNode root = new TreeNode(preorder[preStart]);
     int inIndex = 0;
     for(int i = inStart; i <= inEnd; i++){
         if(inorder[i] == root.val)  inIndex = i;
     }
     root.left = buildTree(inStart, inIndex - 1, preStart + 1, preorder, inorder);
     root.right = buildTree(inIndex + 1, inEnd, preStart + inIndex - inStart + 1, preorder, inorder);
     return root;
 }
```

173. Binary Search Tree Iterator: https://leetcode.com/problems/binary-search-tree-iterator/

二叉树迭代器, 使用 stack 实现迭代访问即可, 每次访问栈顶, 并把 node.right 作为 root, 所有左节点压入

```java
class BSTIterator {
    Deque<TreeNode> stack;

    public BSTIterator(TreeNode root) {
        this.stack = new ArrayDeque<>();
        while(root != null){
            stack.push(root);
            root = root.left;
        }
    }
    
    public int next() {
        TreeNode curr = stack.pop();
        TreeNode right = curr.right;
        while(right != null){
            stack.push(right);
            right = right.left;
        }
        return curr.val;
    }
    
    public boolean hasNext() {
        return !stack.isEmpty();
    }
}
```
1586. Binary Search Tree Iterator II: https://leetcode.com/problems/binary-search-tree-iterator-ii/

新增操作 prev, 增加一个存 prev 的stack 和 记录visited 的 set, 保证每个node 在 next 中的右子树只新增一次

```java
class BSTIterator {
    Deque<TreeNode> stack = new ArrayDeque<>();
    Deque<TreeNode> back = new ArrayDeque<>();
    Set<TreeNode> visited = new HashSet<>();

    public BSTIterator(TreeNode root) {
        addAllLeft(root);
    }
    
    public boolean hasNext() {
        return !stack.isEmpty();
    }
    
    public int next() {
        TreeNode curr = stack.pop();
        if(!visited.contains(curr) && curr.right != null){
            addAllLeft(curr.right);
        }
        back.push(curr);
        visited.add(curr);
        return curr.val;
    }
    
    public boolean hasPrev() {
        return !back.isEmpty() && back.size() > 1;
    }
    
    public int prev() {
        TreeNode curr = back.pop();
        stack.push(curr);
        return back.peek().val;
    }
    
    public void addAllLeft(TreeNode node){
        while(node != null){
            stack.push(node);
            node = node.left;
        }
    }
}
```