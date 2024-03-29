# ListNode

## Merge Two Sorted Lists
https://leetcode.com/problems/merge-two-sorted-lists/

双指针遍历, 使用dummy

```java
public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
    ListNode node1 = list1, node2 = list2;
    ListNode dummy = new ListNode();
    ListNode head = dummy;
    while(node1 != null && node2 != null){
        if(node1.val >= node2.val){
            head.next = node2;
            node2 = node2.next;
        }else{
            head.next = node1;
            node1 = node1.next;
        }
        head = head.next;
    }
    if(node2 != null) head.next = node2;
    if(node1 != null) head.next = node1;
    return dummy.next;
}
```

## Merge k Sorted Lists
https://leetcode.com/problems/merge-k-sorted-lists/

核心三点: 使用 PQ 实现一个 ListNode.val 单增的 优先队列 + 利用 List 的 next 特性实现延迟访问 + dummyHead

先声明一个单增PQ, 把所有 list 的头都放到 PQ 中, 每次都访问 PQ.poll, 把结果增加到 head 后, 如果该 node 依然有next 则新增到 PQ 直到所有 list 访问完毕

```java
public ListNode mergeKLists(ListNode[] lists) {
    if(lists == null || lists.length == 0)  return null;
    ListNode head = new ListNode();
    ListNode dummyHead = head;
    PriorityQueue<ListNode> pq = new PriorityQueue<>((ListNode o1, ListNode o2) -> (o1.val - o2.val));
    for(ListNode listHead : lists){
        if(listHead != null)    pq.offer(listHead);
    }
    while(!pq.isEmpty()){
        head.next = pq.poll();
        head = head.next;
        if(head.next != null)   pq.offer(head.next);
    }
    return dummyHead.next;
}
```

# Cycle detect
## Linked List Cycle 
https://leetcode.com/problems/linked-list-cycle/

使用快慢双指针, 找cycle, 数学原理: $2x - looplength * cycles = x;$ for x = 慢指针路径, looplength = 环的长度, cycles = 任意圈数

始终存在: $x = looplength * cycles;$ for any x and any cycles

```java
public boolean hasCycle(ListNode head) {
    ListNode fast = head, slow = head;
    while(fast != null && fast.next != null && fast.next.next != null){
        fast = fast.next.next;
        slow = slow.next;
        if(fast == slow)    return true;
    }
    return false;
}
```

## Linked List Cycle II 
https://leetcode.com/problems/linked-list-cycle-ii/

$fast = 2 * slow;$ 快指针路径是慢的2倍

$fast = a + (b+c) + b;$ 快慢相遇时, 快比慢多走N圈

$slow = a + b$

$2(a + b)  = a + 2b + c -> a = c$

slow 从相遇点, fast 从 head, 二者同时走, 再次相遇刚好在环入口
![https://assets.leetcode-cn.com/solution-static/142/142_fig1.png](https://assets.leetcode-cn.com/solution-static/142/142_fig1.png)

```java
public ListNode detectCycle(ListNode head) {
    if(head == null)    return null;
    ListNode fast = head, slow = head;
    while(fast.next != null && fast.next.next != null){
        fast = fast.next.next;
        slow = slow.next;
        if(fast == slow){
            fast = head;
            while(slow != fast){
                slow = slow.next;
                fast = fast.next;
            }
            return slow;
        }
    }
    return null;
}
```

## Remove Duplicates from Sorted List 
https://leetcode.com/problems/remove-duplicates-from-sorted-list/

如果node.val == node.next.val 让 node.next 后移1位

```java
public ListNode deleteDuplicates(ListNode head) {
    ListNode node = head;
    while(node != null){
        if(node == null)    break;
        if(node.next != null && node.val == node.next.val){
            node.next = node.next.next;
        }else{
            node = node.next;
        }
    }
    return head;
}
```

## Remove Duplicates from Sorted List II 
https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/

使用dummy作为前置头, pre 和 head 双指针, head 作为 访问Node, pre 作为 链接Node, pre = dummy 实现初始化时在 head 前, 同时 pre.next = head 实现了 pre.next 的重写; 

```java
public ListNode deleteDuplicates(ListNode head) {
    ListNode dummy = new ListNode();
    ListNode pre = dummy;
    dummy.next = head;
    while(head != null && head.next != null){
        if(head.val == head.next.val){
            while(head != null && head.next != null && head.val == head.next.val){
                head = head.next;
            }
            head = head.next;
            pre.next = head;
        }else{
            pre = pre.next;
            head = head.next;
        }
    }
    return dummy.next;
}
```

## Add Two Numbers 
https://leetcode.com/problems/add-two-numbers/

使用carry计算当前位的和, 判断是否进位, 是否有上一位进

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode dummy = new ListNode();
    ListNode node = dummy;
    int carry = 0;
    boolean addOne = false;
    
    while(l1 != null || l2 != null || addOne){
        if(l1 != null){
            carry += l1.val;
            l1 = l1.next;
        }
        if(l2 != null){
            carry += l2.val;
            l2 = l2.next;
        }
        if(addOne){
            carry += 1;
            addOne = false;
        }
        if(carry >= 10){
            carry -= 10;
            addOne = true;
        }
        ListNode next = new ListNode(carry);
        carry = 0;
        node.next = next;
        node = node.next;
    }
    
    return dummy.next;
}
```

## Rotate List  
https://leetcode.com/problems/rotate-list/

先记录list长度并访问tail node, 把tail和head连一起, tail再走 $count - k \% count$ 抵达截断位置

```java
public ListNode rotateRight(ListNode head, int k) {
    if(head == null || k == 0)    return head;
    
    ListNode tail = head;
    int count = 1;
    while(tail.next!=null){
        tail = tail.next;
        count += 1;
    }
    tail.next = head;
    
    for(int i = 0; i < count - k % count; i++){
        tail = tail.next;
    }
    ListNode newHead = tail.next;
    tail.next = null;
    
    return newHead;
}
```

## Copy List with Random Pointer
https://leetcode.com/problems/copy-list-with-random-pointer/

先在原链上的每个original node后面新增要复制的copied node, 然后将 copied node 的random链接, 最后拆链, 拆为 original list 和 copied list

![https://raw.githubusercontent.com/hot13399/leetcode-graphic-answer/master/138.%20Copy%20List%20with%20Random%20Pointer.jpg](https://raw.githubusercontent.com/hot13399/leetcode-graphic-answer/master/138.%20Copy%20List%20with%20Random%20Pointer.jpg)

```java
public Node copyRandomList(Node head) {
    if(head == null)    return head;
    Node node = head;
    
    while(node != null){
        Node next = node.next;
        Node newOne = new Node(node.val);
        node.next = newOne;
        newOne.next = next;
        node = next;
    }
    
    node = head;
    while(node!=null){
        node.next.random = node.random == null? null:node.random.next;
        node = node.next.next;
    }
    
    node = head;
    Node ori = head;
    Node copy = head.next;
    Node copyHead = copy;
    while(copy.next!= null){
        node.next = node.next.next;
        node = node.next;
        copy.next = copy.next.next;
        copy = copy.next;
    }
    node.next = node.next.next;

    return copyHead;
}
```

## Swapping Nodes in a Linked List 
https://leetcode.com/problems/swapping-nodes-in-a-linked-list/

一个访问双端 k-th 的 One-pass trick: 在访问到 k-th node 的时候, 把 node2 设置为 head, 这样当 node 访问到结尾的时候, node2 刚好在 n-k th 的位置, 实现 One-pass

```java
public ListNode swapNodes(ListNode head, int k) {
    ListNode swap1 = null, swap2 = null;
    for(ListNode node = head; node != null; node = node.next){
        swap2 = swap2 == null ? null : swap2.next;
        if(--k==0){
            swap1 = node;
            swap2 = head;
        }
    }
    int val = swap1.val;
    swap1.val = swap2.val;
    swap2.val = val;
    return head;
}
```

## 19. Remove Nth Node From End of List 
https://leetcode.com/problems/remove-nth-node-from-end-of-list/

和上面拿到题一样的思想, 但是注意使用 dummy before head 进行访问, 从而避免 n = 1 和 list.length = 1 时候的越界问题

```java
public ListNode removeNthFromEnd(ListNode head, int n) {        
    ListNode dummy = new ListNode(), fast = dummy, slow = dummy;
    dummy.next = head;
    while(n>=0){
        fast = fast.next; 
        n--;
    }
    while(fast != null){
        fast = fast.next;
        slow = slow.next;
    }
    slow.next = slow.next.next;
    return dummy.next;
}
```

## 143. Reorder List
https://leetcode.com/problems/reorder-list/

使用 Stack 存储所有 Node, 

```java
public void reorderList(ListNode head) {
    ListNode node = head;
    Deque<ListNode> stack = new ArrayDeque<>();
    while(node != null){
        stack.push(node);
        node = node.next;
    }
    ListNode temp = head;
    int n = stack.size();
    for(int i = 0; i < n/2; i++){
        ListNode tail = stack.pop();
        tail.next = temp.next;  // ptr to SAME next
        temp.next = tail;
        temp = temp.next.next;
    }
    temp.next = null;   // remove potential cycle
    return;
}
```


# Reverse Linked List
## 206. Reverse Linked List
https://leetcode.com/problems/reverse-linked-list/

使用dummy 作为 new ListNode 的 tail, 并且把 original next 指向 dummy, 实现反转

```java
public ListNode reverseList(ListNode head) {
    ListNode dummy = null;
    while(head != null){
        ListNode next = head.next;
        head.next = dummy;
        dummy = head;
        head = next;
    }
    return dummy;
}
```

## 92. Reverse Linked List II
https://leetcode.com/problems/reverse-linked-list-ii/

使用 stack 实现 O(N + (right - left)) 的 iterative solution

```java
public ListNode reverseBetween(ListNode head, int left, int right) {
    if(head == null)    return null;
    left -= 1;
    
    Deque<ListNode> stack = new ArrayDeque<>();             // stack, store the node 
    ListNode prev = new ListNode(-1, head);
    
    // find prev and last in one pass
    ListNode dummy = prev, node = head, last = head;
    int cnt = 0;
    while(node != null){
        if(cnt < left){
            prev = prev.next;
        }
        if(cnt < right){
            last = last.next;
        }
        if(cnt >= left && cnt < right){
            stack.push(node);
        }
        cnt++;
        node = node.next;
    }

    // rebuild the innner nodes
    while(!stack.isEmpty()){
        prev.next = stack.pop();
        prev = prev.next;
    }
    prev.next = last;
    
    return dummy.next;
}
```

高级 One pass, 使用三个 ListNode: prev, curr, tail. prev 是在 反转List开头的前一个, curr 是当前节点, tail 是尾节点. 核心: 保持 prev 和 curr 不变, prev 的下一个始终是 tail, curr 的下一个始终是 最新的tail, 通过将 tail 放到 prev 后面, curr 指向最后面, 进行翻转

1. curr 的 next 指向 tail 的 next, 保证链表能够访问到下一个node;    
2. tail 的 next 指向 prev 的 next, 表示 tail 和 curr 之间的 next 反向;
3. prev 的 next 指向 tail, 将本来是 prev 和 curr 之间的 next 变为 prev 和 tail 之间的 next, 从而实现 prev -> tail -> curr 的反向;
4. tail 变为 curr 的 next, 表示为走到下一组;
5. 此时, 

```java
public ListNode reverseBetween(ListNode head, int left, int right) {
    if(head == null)    return null;

    ListNode prev = new ListNode(-1, head);
    ListNode dummy = prev, curr = head, tail = null;
    
    for(int i = 0; i < left - 1; i++)
        prev = prev.next;
    
    curr = prev.next;
    tail = curr.next;
    
    for(int i = 0; i < right - left; i++){
        curr.next = tail.next;  // 2 -> 4           
        tail.next = prev.next;  // 3 -> 2
        prev.next = tail;       // 1 -> 3
        tail = curr.next;       // tail -> 4
        // 1 -> 3 -> 2 -> 4 -> 5
    }   
    
    return dummy.next;
}
```

## 86. Partition List
https://leetcode.com/problems/partition-list/

双指针, 一个是小指针, 一个是大指针, 拼接即可

```java
 public ListNode partition(ListNode head, int x) {
     ListNode smaller = new ListNode(-1), bigger = new ListNode(-1);
     ListNode smallHead = smaller, bigHead = bigger;
     while(head != null){
         if(head.val >= x){
             bigger.next = head;
             bigger = bigger.next;
         }else{
             smaller.next = head;
             smaller = smaller.next;
         }
         head = head.next;
     }
     smaller.next = bigHead.next;
     bigger.next = null;
     return smallHead.next;
 }
```