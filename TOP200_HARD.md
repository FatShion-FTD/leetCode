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

## 42. Trapping Rain Water
https://leetcode.com/problems/trapping-rain-water/

接雨水, 木桶效应, 看最低的木板, 两种办法: 
1. 记录从右到左最大高度, 取min(leftMax, rightMax) - cur 即可
2. 双指针, 首先找到可以开始接雨水的位置 left 和 right, 同上理使用 较小的高度 - 当前侧更小的cur高度 = 当前cur位置装水量; 通过维护 left 和 right, 确保能装上水即可

```java
public int trap(int[] height) {
  // 1. 2-traversal   O(n) O(n)
  int[] right2left = new int[height.length];
  int res = 0, left2right = 0;
  
  for(int i = height.length - 1; i >= 0; i--){
      right2left[i] = Math.max(height[i], right2left[Math.min(height.length - 1, i + 1)]);
  }
  
  for(int i = 0; i < height.length; i++){
      left2right = Math.max(height[i], left2right);
      res += Math.min(right2left[i], left2right) - height[i];
  }
  return res;
  
  // 2. 2 ptr O(n) O(1)
  int left = 0, right = height.length - 1, res = 0;
  // find first left & right 2 wall
  while(left < right && height[left] <= height[left+1])   left++;
  while(left < right && height[right] <= height[right-1])   right--;
  
  while(left < right){
      int leftHeight = height[left];
      int rightHeight = height[right];
      if(leftHeight < rightHeight){
          while(left < right && height[++left] <= leftHeight){
              res += leftHeight - height[left];
          }
      }else{
          while(left < right && height[--right] <= rightHeight){
              res += rightHeight - height[right];
          }
      }
  }
  return res;
}
```

## 135. Candy
https://leetcode.com/problems/candy/

方法1: 同接雨水的两遍双向遍历, 但是此时条件为, 从左往右, 遇到更大的rating:    
num[i+1] = num[i] + 1     
从右往左, 则为遇到更大的rating, 要么保留原数目(> num[i] + 1), 要么+1:    
num[i-1] = max(num[i-1], num[i] + 1)

```java
 public int candy(int[] ratings) {
     int n = ratings.length;
     int[] candies = new int[n];
     Arrays.fill(candies, 1);
     
     for(int i = 0; i < n - 1; i++){
         if(ratings[i] < ratings[i+1]){
             candies[i+1] = candies[i] + 1;        
         }
     }
     
     for(int i = n - 1; i > 0; i--){
         if(ratings[i] < ratings[i-1]){
             candies[i-1] = Math.max(candies[i-1], candies[i] + 1);
         }
     }
     int res = 0;
     for(int num : candies)  res += num;

     return res;
 }
```

方法2, O(n) time + O(1) space. 核心三点:
1. If rate[i] == rate[i+1], 则 num[i+1]可以置 1;
2. If rate[i] < rate[i+1], 则 num[i+1] = num[i] + 1, 并不是强制 + 1, 可以比 num[i] 大很多;
3. If rate[i] > rate[i+1], 因为不知道 连续下降的次数, 可能最后的人是 -1, 所以记录连续下降的次数, 山谷为1, 则其为长度为 cnt 的到 1 的等差数列和, cnt 和山顶的 pre 比较, 判断山顶元素是否大于 cnt, 补齐 cnt - pre + 1即可 

```java
 public int candy(int[] ratings) {
     int n = ratings.length;
     int cnt = 0, pre = 1, res = 1;
     for(int i = 1; i < n; i++){
         if(ratings[i] >= ratings[i-1]){
             if(cnt > 0){    // 结束单减
                 res += cnt * (cnt + 1) / 2; // 从1到 n 的cnt长等差数列和
                 if(cnt >= pre)  res += cnt - pre + 1;   // 补齐
                 cnt = 0;
                 pre = 1;
             }
             pre = ratings[i] == ratings[i-1] ? 1 : pre + 1; // 如果相等, 直接置1
             res += pre;
         }else{
             cnt++;
         }
     }
     if(cnt > 0){
         res += cnt * (cnt + 1) / 2;
         if(cnt >= pre)  res += cnt - pre + 1;
     }
     return res;
 }
```


## 76. Minimum Window Substring
https://leetcode.com/problems/minimum-window-substring/

滑窗 + MAP, 注意滑窗的条件:
1. 第一层 Loop 只走滑窗 right, 并更新滑窗内容;
2. 第二层 Loop 只在当一个有效的滑窗构建完毕, 记录当前信息作为 res, 并只走 left, 更新滑窗内容.
3. 本题使用 trick: String Pattern 的长度作为 判断滑窗是否构建完毕的指标. 

```java
public String minWindow(String s, String t) {
    // map + sliding window
    // s's sliding window contains all t's char firstly, how? 
    if (s == null || s.length() == 0)
        return "";
    if (t == null || t.length() == 0) {
        return s;
    }
    
    Map<Character, Integer> map = new HashMap<>();
    Map<Character, Integer> window = new HashMap<>();
    for (char c : t.toCharArray()) {
        map.put(c, map.getOrDefault(c, 0) + 1);
        window.put(c, 0);
    }
    
    int left = 0, right = 0, start = 0, len = Integer.MAX_VALUE, remain = t.length();
    
    
    while (right < s.length()) {
        // right expand
        char rChar = s.charAt(right);
        if (map.containsKey(rChar)) {          // right is part of t
            if (window.get(rChar) < map.get(rChar))
                remain--;
            window.put(rChar, window.get(rChar) + 1);
        }
        right++;
        while (remain == 0) {
            if (right - left < len) {
                len = right - left;
                start = left;
            }
            char lChar = s.charAt(left);
            if (map.containsKey(lChar)) {
                window.put(lChar, window.get(lChar) - 1);
                if (window.get(lChar) < map.get(lChar))
                    remain++;
            }
            left++;
        }
    }
    
    return len == Integer.MAX_VALUE ? "" : s.substring(start, start + len);
}
```

## 340. Longest Substring with At Most K Distinct Characters
https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/

和上面一道题基本原理一样, map + 滑窗 的处理, 外 loop 滑 right, 内 loop 滑 left. 但是滑动条件是, substring 的 size < k.    
(优化计算res)  注意 res 计算位置, 一. 只有 size > k 更新, 二. 结束时候如果 size <= k, 更新

```java
 public int lengthOfLongestSubstringKDistinct(String s, int k) {
     // right move until 
     if (s == null || s.length() == 0 || k == 0)
         return 0;
     
     Map<Character, Integer> map = new HashMap<>();
     int left = 0, right = 0, res = -1;
     
     while (right < s.length()) {
         char rChar = s.charAt(right);
         map.put(rChar, map.getOrDefault(rChar, 0) + 1);
         if (map.size() > k) {
             res = Math.max(res, right - left);
             while (left < right && map.size() > k) {
                 int cnt = map.get(s.charAt(left));
                 if (cnt == 1)
                     map.remove(s.charAt(left));
                 else
                     map.put(s.charAt(left), cnt - 1);
                 left++;
             }
         }
         right++;
     }
     if (map.size() <= k)
         res = Math.max(res, right - left);
     return res == -1 ? s.length() : res;
 }
```