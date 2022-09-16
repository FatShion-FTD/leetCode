# 线段树
类似 BST, 但是 存储一个区间, 常用于 O(logN) 时间的区间查询, TreeNode 包含4个主要信息:     
left, right, leftNode, rightNode     
其中 left 和 right 为区间的左右边界, 我习惯使用 [left, right) 左闭右开区间      
使用二分的办法 构建, 更新, 查找树     
```java
class Node{
    int left, right, sum;
    Node leftNode, rightNode;
    public Node(int left, int right){
        this.left = left;
        this.right = right;
    }
}

// build
int mid = left + (right - left) / 2;
TreeNode leftNode = build(left, mid);
TreeNode rightNode = build(mid, right);

// update (index, val)
if (node.right - node.left == 1)
    node.sum = val;

if (index < mid) {
    update(leftNode, index, val);
} else {
    update(rightNode, index, val);
}

// query (left, right)
if (node.left >= left && node.right <= right + 1)   // 因为右区间为开, 所以 + 1
    return node.sum;

if (left >= mid) {
    query(node.rightNode, left, right);
} else if (right < mid) {
    query(node.leftNode, left, right);
} else {
    query(node.leftNode, left, mid);
    query(node.rightNode, mid, right);
}
```


## 307. Range Sum Query - Mutable
https://leetcode.com/problems/range-sum-query-mutable/


```java
class NumArray {
    // build a segment tree, which stores the [0, n] index and their sum, 
    // update: find the index, and update all the node on this path
    // sumRange: search for the segment, and return all their sum
    
    Node root;
    
    public NumArray(int[] nums) {
        this.root = new Node(0, nums.length);
        build(nums, root);
    }
    
    public void update(int index, int val) {
        update(root, index, val);
    }
    
    private void update (Node node, int i, int val) {
        if (node.right - node.left == 1) {
            node.sum = val;
        } else {
            int mid = node.left + (node.right - node.left) / 2;
            if (i < mid) {
                update(node.leftNode, i, val);
            } else {
                update(node.rightNode, i, val);
            }
            node.sum = node.leftNode.sum + node.rightNode.sum;
        }
    }
    
    public int sumRange(int left, int right) {
        return find(root, left, right);
    }
    
    private int find(Node node, int left, int right) {
        if (node.left >= left && node.right <= right + 1) {
            return node.sum;
        } else {
            int mid = node.left + (node.right - node.left) / 2;
            if (right < mid) {             // entriely at left subTree
                return find(node.leftNode, left, right);
            } else if (left >= mid) {       // entirely at right subTree
                return find(node.rightNode, left, right);
            } else {
                return find(node.leftNode, left, right) + find(node.rightNode, left, right);
            }
        }
    }
    
    
    private int build (int[] nums, Node node) {
        int left = node.left, right = node.right;
        if (right - left == 1) {         // leaf
            return node.sum = nums[left];
        } else if (right - left > 1) {
            int mid = left + (right - left) / 2;
            Node leftNode = new Node(left, mid);
            Node rightNode = new Node(mid, right);
            node.leftNode = leftNode;
            node.rightNode = rightNode;
            node.sum = build(nums, leftNode) + build(nums, rightNode);
        }
        return node.sum;
    }
    
    class Node {
        int left, right;
        int sum;
        Node leftNode, rightNode;
        public Node(int left, int right){       // [left, right)
            this.left = left;
            this.right = right;
        }
    }
    
    
}
```



