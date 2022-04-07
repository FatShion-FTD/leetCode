# DoubleLinkedList  双链表
## LRU Cache

https://leetcode.com/problems/lru-cache/

核心: 双链表 + dummy head/tail + hashMap; 使用hashMap储存对应的 key-value pair, 使用 双链表 连接实现O(1)操作: 链表的 head 始终存储最新操作的key, tail 存最后的操作

get: 如果存在 key, 则通过 map 返回 value, 并将 node 放到 head, 表示最新

put: 如果存在 key, 则直接更新 value 并将 node 放到 head; 
如果不存在 key, 则新建一个 node, 放到 head, 判读是否 溢出, 如果溢出则把 tail 的 node 删了(带map)

```java
class LRUCache {
    class DLinkedNode{
        DLinkedNode prev;
        DLinkedNode next;
        int key;
        int value;
        public DLinkedNode(){}
        public DLinkedNode(int key, int val){this.key = key;    this.value = val;}
    }
    
    Map<Integer, DLinkedNode> cache = new HashMap<>();
    int size, capacity;
    DLinkedNode head, tail;
    
    public LRUCache(int capacity) {
        size = 0;
        this.capacity = capacity;
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head.next = tail;
        tail.prev = head;
    }
    
    public int get(int key) {
        DLinkedNode node = cache.get(key);
        if(node == null)    return -1;
        moveToHead(node);
        return node.value;
    }
    
    public void put(int key, int value) {
        DLinkedNode node = cache.get(key);
        if(node == null){
            DLinkedNode newNode = new DLinkedNode(key, value);
            cache.put(key, newNode);
            addToHead(newNode);
            size++;
            if(size > capacity){
                DLinkedNode tail = removeTail();
                cache.remove(tail.key);
                size--;
            }
        }else{
            node.value = value;
            moveToHead(node);
        }
    }
    
    public void moveToHead(DLinkedNode node){
        removeNode(node);
        addToHead(node);
    }
    
    public void addToHead(DLinkedNode node){
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }
    
    public void removeNode(DLinkedNode node){
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
    
    public DLinkedNode removeTail(){
        DLinkedNode res = tail.prev;
        removeNode(res);
        return res;
    }
}
```

Flatten a Multilevel Doubly Linked List: https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/

在 node 有 child 的情况下, 直接走到最后把 最后一个和 node.next 连起来, node 和 node.child 连起来即可

为什么不用考虑递归: 因为 链表的特性, 在上述方法连接一层后, 再往后走, 就能碰到第二层, 第三层以此类推, 则不用考虑递归

```java
public Node flatten(Node head) {
    Node node = head;
    while(node != null){
        if(node.child == null){
            node = node.next;
        }else{
            Node temp = node.child;
            while(temp.next!=null){
                temp = temp.next;
            }
            temp.next = node.next;
            if(node.next != null)   node.next.prev = temp;
            node.next = node.child;
            node.child.prev = node;
            node.child = null;
        }
    }
    return head;
}
```