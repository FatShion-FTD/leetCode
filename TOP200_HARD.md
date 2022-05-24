# TOP200 HARD 实录
## 前两北的HARD是背都要被下来的!!
### 25. Reverse Nodes in k-Group: https://leetcode.com/problems/reverse-nodes-in-k-group/

分为K组的反转链表, 基础版: 分组, 重建, 处理特殊, O(n), O(n)
```java
public ListNode reverseKGroup(ListNode head, int k) {
    if(head == null || k <= 1)  return head;
    
    // create groups
    ListNode node = head;
    int count = 0;
    List<ListNode> cache = new ArrayList<>();
    List<List<ListNode>> groups = new ArrayList<>();
    while(node!=null){
        cache.add(node);
        count++;
        node = node.next;
        if(count % k == 0){
            groups.add(new ArrayList<>(cache));
            cache = new ArrayList<>();
        }
    }
    
    // rebuild list
    ListNode dummy = new ListNode();
    node = new ListNode();
    dummy.next = node;
    for(int i = 0; i < groups.size(); i++){
        List<ListNode> group = groups.get(i);
        for(int j = k-1; j >= 0; j--){
            ListNode temp = group.get(j);
            temp.next = null;
            node.next = temp;
            node = node.next;
        }
    }
    
    // add the tail
    if(!cache.isEmpty()) node.next = cache.get(0);
    return dummy.next.next;
}
```

进阶版: 每K个一组, 反转链表区间, 主要在于反转函数:

0->1->2->3->4 , begin = 0, tail = 4, 反转区间1->2->3, 先记录 first = 1, 然后反转成为 0<-1<-2<-3->4 , 此时 curr = 4, prev = 3, 将 dummy head 的 next 重新指向 0->3, 
```java
public ListNode reverseKGroup(ListNode head, int k) {
    if(head == null || head.next == null || k == 1)  return head;
    ListNode dummy = new ListNode(-1);
    dummy.next = head;
    ListNode begin = dummy; // begin在反转区间前
    int index = 0;
    while(head != null){
        index++;
        if(index % k == 0){
            begin = reverse(begin, head.next);
            head = begin.next;
        }else{
            head = head.next;
        }
    }
    return dummy.next;
}
 /**
    * Reverse a link list between begin and end exclusively
    * an example:
    * a linked list:
    * 0->1->2->3->4->5->6
    * |           |   
    * begin       end
    * after call begin = reverse(begin, end)
    * 
    * 0->3->2->1->4->5->6
    *          |  |
    *      begin end
    * @return the reversed list's 'begin' node, which is the precedence of node end
    */
private ListNode reverse(ListNode head, ListNode tail){
    ListNode curr = head.next;  // 当前节点
    ListNode next, first;
    ListNode prev = head;   // 前一节点
    first = curr;           // 头节点
    while(curr!= tail){
        next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    head.next = prev;
    first.next = curr;
    return first;
}
```

### 32. Longest Valid Parentheses: https://leetcode.com/problems/longest-valid-parentheses/

1. 先遍历整个str, 记录所有不成对bracket的位置, 则所有GAP就是最长子集了
2. 再每次从栈顶拿出, 记录GAP, 比较得到最大GAP

```java
public int longestValidParentheses(String s) {
    if(s.length() == 0) return 0;
    Deque<Integer> stack = new ArrayDeque<>();
    for(int i = 0; i < s.length(); i++){
        if(s.charAt(i) == '('){
            stack.push(i);
        }else{
            // stack 不为空, 如果栈顶为(, 一对, 去掉, 否则加入 ) index
            if(!stack.isEmpty()){
                if(s.charAt(stack.peek()) == '(') stack.pop();
                else stack.push(i);
            }else stack.push(i);
        }
    }
    int res = 0;
    if(stack.isEmpty()) return s.length();
    else{
        int left = 0, right = s.length();
        while(!stack.isEmpty()){
            left = stack.pop();
            res = Math.max(res, right - left - 1);
            right = left;
        }
        res = Math.max(left, res);
    }
    return res;
}
```