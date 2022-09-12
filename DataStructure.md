# 数据结构
# HashSet 实现

hash set 好复杂啊, 复制的, 看不懂

```java
class MyHashSet {
    List<Integer>[] container = null;
    int cap = 1000;
    double loadFactor = 0.75;
    int count = 0; 
    /** Initialize your data structure here. */
    public MyHashSet() {
        container = new LinkedList[cap];
    }
    
    public void add(int key) {
        if(contains(key)) return;
        if(loadFactor*cap == count){
            count = 0;
            //rehash
            cap *= 2;
            List<Integer>[] oldC = container;
            container = new LinkedList[cap];
            for(int i=0; i < oldC.length; i++){
                List<Integer> list = oldC[i];
                if(list != null){
                    for(int entry : list)
                       this.add(entry); 
                }
            }
        }
        int hash = key % cap;
        if(container[hash] == null)
            container[hash] = new LinkedList<>();
        container[hash].add(key);
        ++count;
    }
    
    public void remove(int key) {
        int hash = key % cap;
        List<Integer> list = container[hash];
        if(list != null){
            Iterator<Integer> itr = list.iterator();
            while(itr.hasNext())
                if(itr.next() == key){
                    itr.remove();
                    --count;
					break;
                }
        }
    }
    
    /** Returns true if this set contains the specified element */
    public boolean contains(int key) {
        int hash = key % cap;
        List<Integer> list = container[hash];
        if(list != null){
            Iterator<Integer> itr = list.iterator();
            while(itr.hasNext())
                if(itr.next() == key)
                    return true;
        }
        return false;
    }
}
```

## HashTable 实现

使用 Node 进行 hashtable 实现, Node 实际是 Map.Entry. 使用 Integer.hashCode 直接获取 hash code, 然后对 node.length 取模

```java
class MyHashMap {

        final ListNode[] nodes = new ListNode[10_000];

        public void put(int key, int value){
            int i = idx(key);
            if(nodes[i] == null)
                nodes[i] = new ListNode(-1, -1);
            ListNode prev = find(nodes[i], key);
            if(prev.next == null)
                prev.next = new ListNode(key, value);
            else prev.next.val = value;
        }

        public int get(int key){
            int i = idx(key);
            if(nodes[i] == null)
                return -1;
            ListNode node = find(nodes[i], key);
            return node.next == null ? -1 : node.next.val;
        }

        public void remove(int key){
            int i = idx(key);
            if(nodes[i] != null){
                ListNode prev = find(nodes[i], key);
                if(prev.next != null)
                    prev.next = prev.next.next;
            }
        }

        int idx(int key){return Integer.hashCode(key) % nodes.length;}

        ListNode find(ListNode bucket, int key){
            ListNode node = bucket, prev = null;
            for(; node != null && node.key != key; node = node.next)
                prev = node;
            return prev;
        }

        class ListNode{
            int key, val;
            ListNode next;

            ListNode(int key, int val){
                this.key = key;
                this.val = val;
            }
        }
}
```

# 栈
Design a Stack With Increment Operation: https://leetcode.com/problems/design-a-stack-with-increment-operation/

客制化stack, 核心: **前缀和**, 使用 inc 数组维护一个**数据改变的操作**序列 而不是单纯的数字:

inc[4] = 100 代表 前4个都要加100的操作

```java
class CustomStack {
    int n;
    int[] inc;
    Deque<Integer> stack;
    
    public CustomStack(int maxSize) {
        n = maxSize;
        stack = new ArrayDeque<>();
        inc = new int[n];
    }
    
    public void push(int x) {
        if(stack.size() < n)  stack.push(x);
    }
    
    public int pop() {
        int i = stack.size() - 1;
        if(i < 0)   return -1;
        if(i > 0)   inc[i-1] += inc[i];
        int res = stack.pop() + inc[i];
        inc[i] = 0;
        return res;
    }
    
    public void increment(int k, int val) {
        int i = Math.min(k, stack.size()) - 1;
        if(i >= 0)  inc[i] += val;
    }

    public void sum(int k){
        int sum = 0;
        for(int i = 0; i < k && k < stack.size() - 1; i++){
            sum += inc[i] + stack.get(i);
        }
        return sum;
    }
}
```

Validate Stack Sequences: https://leetcode.com/problems/validate-stack-sequences/

要检查一个pop list是否符合stack的顺序, 在于: 栈顶是否一直和pop list的第一个元素相同

```java
public boolean validateStackSequences(int[] pushed, int[] popped) {
    Deque<Integer> stack = new ArrayDeque<>();
    int i = 0;
    for(int p : pushed){
        stack.push(p);
        while(!stack.isEmpty() && stack.peek() == popped[i]){
            stack.pop();
            i++;
        }
    }
    return stack.isEmpty();
}
```

Score of Parentheses: https://leetcode.com/problems/score-of-parentheses/

将括号对作为二叉树对待, ()就是值为1的leaf, (()())就是有两个叶子的parent,值为4. 使用curr记录每一层的值, 每次遇到 ( ,进入下一层, curr清零; 遇到 ), 则使用pop掉一个 ( 以退出一层并加上上一层的值, 并 curr * 2, 但 curr 的最小值为 1.

```java
public int scoreOfParentheses(String s) {
    Deque<Integer> stack = new ArrayDeque<>();
    int curr = 0;
    for(char c : s.toCharArray()){
        if(c == '('){
            stack.push(curr);
            curr = 0;
        }else{
            curr = stack.pop() + Math.max(1, 2 * curr);
        }
    }
    return curr;
}
```

Remove Duplicate Letters: https://leetcode.com/problems/remove-duplicate-letters/

使用visited和lastIndex实现单调栈, 将所有不符合字典序且最后出现位置大于当前位置的pop掉, 实现字典序单调栈.

lexicographical order: 符合离散子序列, 且按abcd等字典序排列的

```java
public String removeDuplicateLetters(String s) {
    char[] chars = s.toCharArray();
    StringBuilder sb = new StringBuilder();
    Deque<Character> stack = new ArrayDeque();
    int[] lastIndex = new int[26];
    boolean[] visited = new boolean[26];
    
    for(int i = 0; i < chars.length; i++){
        lastIndex[chars[i] - 'a'] = i;
    }
    
    for(int i = 0; i < chars.length; i++){
        int curAlpha = chars[i] - 'a';
        if(visited[curAlpha])   continue;
        while(!stack.isEmpty() && stack.peek() > chars[i] && i < lastIndex[stack.peek() - 'a']){
            visited[stack.pop() - 'a'] = false;
        }
        stack.push(chars[i]);
        visited[curAlpha] = true;
    }
        
    while(!stack.isEmpty()){
        sb.append(stack.pop());
    }
    
    return sb.reverse().toString();
}
```

Maximum Frequency Stack: https://leetcode.com/problems/maximum-frequency-stack/

使用freqMap记录每个element的freq, 使用stackMap记录每个freq下的stack. 在访问element时, 先通过freqMap确定该element的freq, 再通过freq访问对应stack得到pop的结果. 在push时, 将element push到对应freq的stack中, 如无响应freq则新建stack

```java
class FreqStack {
    Map<Integer, Integer> freqMap;
    Map<Integer, Deque<Integer>> stackMap;
    int maxf;

    public FreqStack() {
        this.freqMap = new HashMap<>();
        this.stackMap = new HashMap<>();
        this.maxf = 0;
    }
    
    public void push(int val) {
        int count = freqMap.getOrDefault(val, 0) + 1;
        freqMap.put(val, count);
        maxf = Math.max(maxf, count);
        if(!stackMap.containsKey(count))  stackMap.put(count, new ArrayDeque<>());
        stackMap.get(count).push(val);
    }
    
    public int pop() {
        int maxVal = stackMap.get(maxf).pop();
        freqMap.put(maxVal, maxf - 1);
        if(stackMap.get(maxf).size() == 0) maxf-=1;
        return maxVal;
    }
}
```

## Data structure training
3Sum: https://leetcode.com/problems/3sum/

双指针, 固定一个num, 然后用类二分法搜索剩余两个; 使用 sort() 和 nums[i] != nums[i-1] 来确保没有重复访问, 使用 i 和 i-1 是因为, i+1 是low, num和low可以相同的. trick: 一旦固定num大于0则退出
```java
public List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    int tot = 0;
    int n = nums.length;
    List<List<Integer>> res = new ArrayList<>();
    
    for(int i = 0; i < n-2; i++){
        if(nums[i] > 0) break;
        if(i == 0 || i > 0 && nums[i] != nums[i-1]){
            int low = i + 1, high = n-1;            
            while(high > low){
                if(nums[high] + nums[low] == -nums[i]){
                    res.add(new ArrayList<>(Arrays.asList(nums[i], nums[high], nums[low])));
                    while(high > low && nums[low] == nums[low+1]) low++;
                    while(high > low && nums[high] == nums[high-1]) high--;
                    low++;
                    high--;
                }else if(nums[high] + nums[low] > -nums[i]){
                    high--;
                }else{
                    low++;
                }
            }
        }
    }
    return res;
}
```

Sort Colors: https://leetcode.com/problems/sort-colors/

dutch partition problem, 使用多指针进行分区:
1. 指针 1 2 3 对应不同分区的位置, 其中 1 和 2 算作已知分区, 从 0 开始, 3 作为未知分区从末尾开始, while(3 > 1)
2. 使用指针 1 作为遍历指, 当 1 的位置是 2 的值, 1 和 2 值互换, 1 和 2 同时 +1, 因为本问题只有三个分区, 3的分区是固定的, 1 和 2 就是互斥的
3. 1 是自己时直接 +1 跳过; 1 是 3 的值时, 3 和 1 互换, 3 指针的位置有了正确的值, 3 指针 -1. 不动 1 指针, 因为 1 指针此时的值不清楚是啥


```java
public void sortColors(int[] nums) {
    int red = 0, white = 0, blue = nums.length - 1;
    while(white <= blue){
        if(nums[white] == 0){   // 互换red white
            int temp = nums[white];
            nums[white++] = nums[red];
            nums[red++] = temp;
        }else if(nums[white] == 1){
            white++;
        }else{
            int temp = nums[white];
            nums[white] = nums[blue];
            nums[blue--] = temp;
        }
    }
    return;
}
```

Merge Intervals: https://leetcode.com/problems/merge-intervals/

在merge intervals时, 先将时间段的开始时间排序, 然后根据last time 的 end 和 curr time 的 start 比较, 判断是否存在覆盖.

难点: Arrays sort中的 lambda表达式, List to Array的办法

```java
 public int[][] merge(int[][] intervals) {
     if(intervals.length == 0 || intervals == null)  return new int[0][];
     
     Arrays.sort(intervals, (i1, i2) -> Integer.compare(i1[0], i2[0]));
     List<int[]> res = new ArrayList<>();
     int start = intervals[0][0], end = intervals[0][1];
     for(int[] interval : intervals){
         if(interval[0] > end){
             res.add(new int[]{start, end});
             start = interval[0];
             end = interval[1];
         }else{
             end = Math.max(end, interval[1]);
         }
     }
     res.add(new int[]{start, end});
     
     return res.toArray(new int[0][]);
 }
```

Rotate Image: https://leetcode.com/problems/rotate-image/

先 transpose, 再 horizontally reflect 水平对调 即可 (顺时针). 逆时针: vertically reflect 左右对调

```java
 public void rotate(int[][] m) {
     int n = m.length;
     for(int i = 0, j = n-1; i < j; i++, j--){
         int[] t = m[i];
         m[i] = m[j];
         m[j] = t;
     }
     for(int i = 0; i < n; i++){
         for(int j = i + 1; j < n; j++){
             int t = m[i][j];
             m[i][j] = m[j][i];
             m[j][i] = t;
         }
     }
     return;
 }
```


# 系统设计
系统设计类型的题目, 考察 data struture 的应用 和 优化


1396. Design Underground System: https://leetcode.com/problems/design-underground-system/

核心 2 点:
1. 使用 2 个Map, 一个 用 route 作为 key, total time 和 count 对作为 value; 一个用 userId 作为 key, checkInStation 和 checkInTime 对作为value.
2. 使用 Pair 数据类型, 和 Map.Entry 有类似函数, 存储 key 和 value, 使用 Pair 存相应的对

```java
class UndergroundSystem {
    Map<String, Pair<Integer, Integer>> checkOutMap = new HashMap<>();  // route- {time, count}
    Map<Integer, Pair<String, Integer>> checkInMap = new HashMap<>();   // Uid - {startStation, checkInTime}
    
    public UndergroundSystem() {}
    
    public void checkIn(int id, String stationName, int t) {
        checkInMap.put(id, new Pair<>(stationName, t));
    }
    
    public void checkOut(int id, String stationName, int t) {
        Pair<String, Integer> pair = checkInMap.get(id);
        String route = pair.getKey() + "_" + stationName;
        int time = t - pair.getValue();
        Pair<Integer, Integer> checkOut = checkOutMap.getOrDefault(route, new Pair<>(0,0));
        checkOutMap.put(route, new Pair<>(checkOut.getKey() + time, checkOut.getValue() + 1));
    }
    
    public double getAverageTime(String startStation, String endStation) {
        String route = startStation + "_" + endStation;
        Pair<Integer, Integer> checkOut = checkOutMap.get(route);
        return (double) checkOut.getKey() / checkOut.getValue();
    }
}
```

# Iterator 迭代器
迭代器的实现: 使用一个 Integer 记录上一时刻的Num即可
```java
class PeekingIterator implements Iterator<Integer> {
    Iterator<Integer> iterator;
    Integer peekNum;
	public PeekingIterator(Iterator<Integer> iterator) {
	    // initialize any member here.
	    this.iterator = iterator;
        this.peekNum = this.iterator.next();
	}
	
    // Returns the next element in the iteration without advancing the iterator.
	public Integer peek() {
        return peekNum;
	}
	
	// hasNext() and next() should behave the same as in the Iterator interface.
	// Override them if needed.
	@Override
	public Integer next() {
        int old = peekNum;
        peekNum = iterator.hasNext() ? iterator.next() : null;
	    return old;
	}
	
	@Override
	public boolean hasNext() {
	    return peekNum != null;
	}
}
```

# 588. Design In-Memory File System
https://leetcode.com/problems/design-in-memory-file-system/

```java

```





## 2349. Design a Number Container System 
https://leetcode.com/problems/design-a-number-container-system/

双Map + TreeSet, 一个存 Index -> Num, 一个存 Num -> Index. 使用 TreeSet 实现快速指定数字增删.    
TreeSet好处: 是 Set, 更是 Sorted Set, 实现是Tree Map, Set 中的数字按照 从小到大 排列, add, remove, contains 等操作都是 O(logN).     
有 ceiling (>= x), floor (<= x), higher (> x), lower (< x), first, last, pollFirst, pollLast 等操作.

```java
class NumberContainers {
    // Map, store the used index and its number -- <Integer, Integer>
    // ReverseMap, number and indexs pair   -- <Integer, TreeSet<Integer>>
    Map<Integer, Integer> index2Num;
    Map<Integer, TreeSet<Integer>> num2Index;
    
    public NumberContainers() {
        index2Num = new HashMap<>();
        num2Index = new HashMap<>();
    }
    
    public void change(int index, int number) {
        if (index2Num.containsKey(index)) {         // update
            int oriNum = index2Num.get(index);
            num2Index.get(oriNum).remove(index);    // remove old num -> index mapping
        }
        index2Num.put(index, number);
        num2Index.putIfAbsent(number, new TreeSet<>());
        num2Index.get(number).add(index);
    }
    
    public int find(int number) {
        if (num2Index.containsKey(number)) {
            if (num2Index.get(number).isEmpty())
                return -1;
            return num2Index.get(number).first();
        } 
        return -1;
    }
}
```

## 2353. Design a Food Rating System
https://leetcode.com/problems/design-a-food-rating-system/

3 MAP + TreeSet. **LeetCode 的 Pair 实现 有TMD大问题**, 不能再用了. 以后但凡遇到 Pair的形式, 直接上 Custom Class.


```java
class FoodRatings {
    Map<String, String> dishSys;
    Map<String, Integer> dishPrice;
    Map<String, TreeSet<Food>> map; // cuisine --SortedList<Pair<String, Integer>>
    
    public FoodRatings(String[] foods, String[] cuisines, int[] ratings) {
        map = new HashMap<>();
        dishSys = new HashMap<>();
        dishPrice = new HashMap<>();
        
        for (int i = 0; i < foods.length; i++) {
            Food f = new Food(foods[i], ratings[i]);
            TreeSet<Food> set = map.getOrDefault(cuisines[i], new TreeSet(new Comparator<Food>(){
         @Override
         public int compare(Food o1, Food o2){
             if (o1.rate == o2.rate)
                 return o1.name.compareTo(o2.name);                 
             
             return o2.rate - o1.rate;
         }
     }));
            set.add(f);
            map.put(cuisines[i], set);
            dishSys.put(foods[i], cuisines[i]);
            dishPrice.put(foods[i], ratings[i]);
        }
    }
    
    public void changeRating(String food, int newRating) {
        String belongSys = dishSys.get(food);
        Integer oldPrice = dishPrice.get(food);
        Food pair = new Food(food, oldPrice);
        map.get(belongSys).remove(pair);
        map.get(belongSys).add(new Food(food, newRating));
        dishPrice.put(food, newRating);
    }
    
    public String highestRated(String cuisine) {
        return map.get(cuisine).first().name;
    }
    
    
    class Food{
        String name;
        int rate;
        
        public Food (String n, int r) {
            name = n;
            rate = r;
        }
    }
}
```



## 114. Flatten Binary Tree to Linked List
https://leetcode.com/problems/flatten-binary-tree-to-linked-list/

将 二叉树 转为 LinkedList, 三种方法:
1. StraightForward
2. in-place, 在 node 的 left subTree 找到最右先序节点 prev, 把 node 的 right subTree 接到 prev 的 right, 再把 node 的 left 转到 right, left 置空即可
3. 递归实现方法二

```java
TreeNode prev = null;

public void flatten(TreeNode root) {
  if (root == null)
      return;
  
//         method1: (straightforward) store all the node in pre-order then re-pointer
  Deque<TreeNode> stack = new ArrayDeque<>();
  List<TreeNode> list = new ArrayList<>();
  stack.push(root);
  
  while (!stack.isEmpty()) {
      TreeNode node = stack.pop();
      list.add(node);
      if (node.right != null)
          stack.push(node.right);
      if (node.left != null)
          stack.push(node.left);
  }
  
  for (int i = 1; i < list.size(); i++) {
      TreeNode node = list.get(i);
      root.left = null;
      root.right = node;
      root = root.right;
  }
  
  // method2: (in-place) find the prev node, make node's right subTree became prev's right subTree, then convert the node left subTree to the right
  
  while (root != null) {
      if (root.left != null) {
          TreeNode prev = root.left;
          while (prev.right != null) {
              prev = prev.right;
          }
          prev.right = root.right;
          root.right = root.left;
          root.left = null;
      }
      root = root.right;
  }
  
  

  // method3: recursion
  flatten(root.right);
  flatten(root.left);
  root.right = prev;
  root.left = null;
  prev = root;
}
```

## 729. My Calendar I
https://leetcode.com/problems/my-calendar-i/

范围查找的经典例题, 使用 TreeMap 实现,只需要查找 start 左右的节点即可, 不要把问题复杂化, 开始节点左边的 end <= start, 开始节点右边的 start >= end 即可, 不用考虑太多

```java
class MyCalendar {
    TreeMap<Integer, Integer> tmap;
    
    public MyCalendar() {
        tmap = new TreeMap<>();
    }
    
    public boolean book(int start, int end) {
        Integer leftStart = tmap.floorKey(start);
        Integer rightStart = tmap.ceilingKey(start);
        
        if ((leftStart == null || tmap.get(leftStart) <= start) &&
           (rightStart == null || rightStart >= end)) {
            tmap.put(start, end);
            return true;
        }
        return false;
    }
}
```

## 2406. Divide Intervals Into Minimum Number of Groups
https://leetcode.com/problems/divide-intervals-into-minimum-number-of-groups/

记录 tail num 和 以 tail num 结尾的个数. 注意 特殊情况: 10 = 2, 来 10, floor 为 null, 需要 getOrDefault(end, 0) + 1

```java
class Solution {
    public int minGroups(int[][] intervals) {
        Arrays.sort(intervals, (o1, o2) -> o1[0] == o2[0] ? o1[1] - o2[1] : o1[0] - o2[0]);
        TreeMap<Integer, Integer> orderedMap = new TreeMap<>();
        int res = 0;
        
        for (int[] interval : intervals) {
            int start = interval[0], end = interval[1];
            Integer floor = orderedMap.lowerKey(start);
            if (floor == null) {
                // 没有 < end的尾节点, 但是存在同样以 end 结尾的
                // 10 = 2, 来 10, floor == null, 10 = 3
                orderedMap.put(end, orderedMap.getOrDefault(end, 0) + 1);    
                res++;
            } else {
                orderedMap.put(floor, orderedMap.get(floor) - 1);
                if (orderedMap.get(floor) == 0)
                    orderedMap.remove(floor);
                orderedMap.put(end, orderedMap.getOrDefault(end, 0) + 1);
            }
        }
        
        return res;
    }
}
```

## 659. Split Array into Consecutive Subsequences
https://leetcode.com/problems/split-array-into-consecutive-subsequences/

2 Map, 使用 freq map 统计出现次数; 使用 seqTails map 记录以当前 key 结尾的 seqence 的数量.       

当 num 不存在 seqTails 时, 说明是新的 seq 的起点, 向后查是否存在有效的 num + 1 和 num + 2 (长度要求, 至少为 3), 存在则更新对应 freq 并建立新的 tail: num + 3.       
如果 num 存在在 seqTail 中, 则说明是一个可行的 seq 新tail, 更新 tail 即可.

```java
public boolean isPossible(int[] nums) {
  // 2 Map: freq and seqTails
  // seqTails counts # of seqs end at Key
  if (nums == null || nums.length == 0)
      return false;
  
  Map<Integer, Integer> freq = new HashMap<>(), seqTails = new HashMap<>();
  
  for (int num : nums) {
      freq.put(num, freq.getOrDefault(num, 0) + 1);
  }
  
  for (int num : nums) {
      if (freq.get(num) <= 0)
          continue;
      
      // num is at tail of existing seqs
      if (seqTails.containsKey(num) && seqTails.get(num) > 0) {        
          // extend seq length and update the tail information
          seqTails.put(num, seqTails.get(num) - 1);                           
          seqTails.put(num + 1, seqTails.getOrDefault(num + 1, 0) + 1);
          
          // new sequence head, need at least 2 more elements  
      } else if (freq.containsKey(num + 1) && freq.get(num + 1) > 0 && 
                 freq.containsKey(num + 2) && freq.get(num + 2) > 0) {  
          
          freq.put(num + 1, freq.get(num + 1) - 1);
          freq.put(num + 2, freq.get(num + 2) - 1);
          seqTails.put(num + 3, seqTails.getOrDefault(num + 3, 0) + 1);
          
      } else 
          return false;
      
      freq.put(num, freq.get(num) - 1);
  }
  
  return true;
}
```