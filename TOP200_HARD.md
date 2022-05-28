# TOP200 HARD 实录
## 前两北的HARD是背都要被下来的!!
### 25. Reverse Nodes in k-Group: https://leetcode.com/problems/reverse-nodes-in-k-group/

10. Regular Expression Matching: https://leetcode.com/problems/regular-expression-matching/

动态规划, 

```java
public boolean isMatch(String s, String p) {
    if(s==null || p==null)  return false;
    int m = s.length();
    int n = p.length();
    boolean[][] dp = new boolean[m+1][n+1];
    dp[0][0] = true;
    // base: s 为空, 能match的pattern, (?*)*, *可以把前一个带走, 如 "a*" = ""
    for(int i = 0; i < n; i++){
        if(p.charAt(i) == '*' && dp[0][i-1]){
            dp[0][i+1] = true;   
        }
    }
    // induction rule: 直接继承: s.charAt(i) == p.charAt(j) || p.charAt(j) == '.'
    // dp[i+1][j] 意味 *为空, 继承上一j, 如 ab* ab; dp[i][j+1]意味 *有重复出现, 如 ab*, abb
    for(int i = 0; i < m; i++){
        for(int j = 0; j < n; j++){
            // ???a(i) - ?a(j)      || ???a(i) - ?.(j)
            if(s.charAt(i) == p.charAt(j) || p.charAt(j) == '.'){
                dp[i+1][j+1] = dp[i][j];
            }else if(p.charAt(j) == '*'){
                if(p.charAt(j-1) != '.' && p.charAt(j-1) != s.charAt(i)){ // s: ??a p: ?b*, 这时 b* = '', 继承 j-2
                    dp[i+1][j+1] = dp[i+1][j-1];
                }else{  // j-1 表示 *把上一位带走了: a* = ''; j 表示 * 为空: a* = a; j+1 表示 * 继承了 s 的上一个: ??a  ?a*, p 后移一位, s指针没动
                    dp[i+1][j+1] = (dp[i+1][j-1] || dp[i+1][j] || dp[i][j+1]);
                }
            }
        }
    }
    return dp[m][n];
}
```

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

354. Russian Doll Envelopes: https://leetcode.com/problems/russian-doll-envelopes/

先排序(1-2增, 2-1减), 后最长单增子序列

```java
 public int maxEnvelopes(int[][] envelopes) {
     if(envelopes.length == 0)   return 0;
     
     Arrays.sort(envelopes, new Comparator<int[]>(){
         @Override
         public int compare(int[] o1, int[] o2){
             if(o1[0] == o2[0])  return o2[1] - o1[1];
             return o1[0] - o2[0];
         }
     });
     
     int[] heights = new int[envelopes.length];
     for(int i = 0; i < envelopes.length; i++)   heights[i] = envelopes[i][1];
     return lengthOfLIS(heights);
 }
 
 public int lengthOfLIS(int[] nums){
     int n = nums.length;
     int[] tail = new int[n];
     int size = 0;
     for(int num : nums){
         int left = 0, right = size;
         while(right > left){
             int mid = left + (right - left) /2;
             if(tail[mid] < num){
                 left = mid + 1;
             }else{
                 right = mid;
             }
         }
         tail[left] = num;
         if(left == size) size++;
     }
     return size;
 }
```

1996. The Number of Weak Characters in the Game: https://leetcode.com/problems/the-number-of-weak-characters-in-the-game/submissions/

上述类似问题, 但是更加trick, 0位单减, 1位单增, 形成Atk从大到小, Def在同Atk下从小到大, 在确保Atk单减的前提下, 则i+1和i一定存在 [i][0] >= [i+1][0]的关系, 保存最大的Def, 如果小于最大Def即weak, 同Atk单增, 确保edge case 如: [1, 4] 和 [1, 10] 

```java
public int numberOfWeakCharacters(int[][] properties) {
  if(properties.length == 0)  return 0;
  
  // sort in descending Atk and ascending Def 
  Arrays.sort(properties, new Comparator<int[]>(){
      @Override
      public int compare(int[] o1, int[] o2){
          if(o1[0] == o2[0])  return o1[1] - o2[1];
          return o2[0] - o1[0];
      }
  });
  int max = Integer.MIN_VALUE, res = 0;
  for(int i = 0; i < properties.length; i++){
      if(properties[i][1] < max)  res++;
      max = Math.max(max, properties[i][1]);
  }
  return res;
}
```