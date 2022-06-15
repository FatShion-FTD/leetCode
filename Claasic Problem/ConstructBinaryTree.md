# 重构二叉树
核心在于 找 根节点 和 左右子节点, 并根据 各种order的特性, 进行 左右子树 的大小计算和index访问, 递归实现 一个子树的生成


## 106. Construct Binary Tree from Inorder and Postorder Traversal

从 postorder 找 root, 从 inorder 找到 root 到右边界的距离 获取右子树大小, postEnd - 1: 右子树的root, postEnd - rightSubTreeSize - 1: 左子树的 root

```java
// property: the last one of postorder is root
// inorder: left root right -> knowing subTree
Map<Integer, Integer> map = new HashMap<>();
public TreeNode buildTree(int[] in, int[] post) {
    for(int i = 0; i < in.length; i++)    map.put(in[i], i);
    return buildTree(post, post.length - 1, 0, in.length - 1);
}
// postEnd: postorder 中 子树范围 的结束
// postStart: postorder 中 子树范围 的开始
// inEnd: inorder 中 子树范围的 结束
private TreeNode buildTree(int[] post, int postEnd, int postStart, int inEnd){
    if(postEnd < postStart)   return null;
    TreeNode root = new TreeNode(post[postEnd]);   // root
    int inIndex = map.get(root.val);
    // 跟据 左中右, inEnd - inIndex = 右子树的大小
    int rightSubTreeSize = inEnd - inIndex;   // right tree size
    // postEnd - 1: 跟据 左右中, -1 得到右子树的根节点, 同时也是新的子树的结束位置
    // postEnd - rightSubTreeSize: 得到右子树开始的位置
    // inEnd不变, 因为仍在当前右子树中
    root.right = buildTree(post, postEnd - 1, postEnd - rightSubTreeSize, inEnd);
    // postEnd - rightSubTreeSize - 1: 跟据 左右中, 去掉右子树再 - 1 = 左子树的根, 也就是左子树的结束位置
    // postStart不变: 因为左子树开始位置不变
    // inIndex - 1: 跟据 左中右, 去掉右子树大小再 - 1 = 左子树的结束位置
    root.left = buildTree(post, postEnd - rightSubTreeSize - 1, postStart, inIndex - 1);
    return root;
}
```

## 105. Construct Binary Tree from Preorder and Inorder Traversal
https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/

和上述类似, 依然使用 inorder 判断, 但是 左子树

```java
Map<Integer, Integer> map = new HashMap<>();
public TreeNode buildTree(int[] pre, int[] in) {
   for(int i = 0; i < in.length; i++)  map.put(in[i], i);
   return buildTree(pre, 0, in.length - 1, 0);
}

private TreeNode buildTree(int[] pre, int preStart, int preEnd, int inStart){
   if(preStart > preEnd)   return null;
   TreeNode root = new TreeNode(pre[preStart]);
   int leftTreeSize = map.get(root.val) - inStart;
   root.left = buildTree(pre, preStart + 1, preStart + leftTreeSize, inStart);
   root.right = buildTree(pre, preStart + leftTreeSize + 1, preEnd, map.get(root.val)+1);
   return root;
}
```
