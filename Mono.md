# 单调栈/队列

使用栈或队列, 维持其中元素最大或者最小, 使得后续元素可以进行 O(1) 的 peek 快速查询 

例子: 最大队列
```java
Deque<Integer> deque = new ArrayDeque<>();
while (right < n) {
    int num = arr[right];
    while(!deque.isEmpty() && arr[q.peekLast()] <= num) {   
        deque.pollLast();                   // 从tail去掉所有小于新值的值
    }
    deque.addLast(num);                     // 新值加入队列
    if(deque.peekFirst() == left) {         // 队列头超过限制
        deque.pollFirst();
    }
}
```




## Problem Set
https://leetcode.com/problems/maximum-number-of-robots-within-budget/discuss/2524838/JavaC%2B%2BPython-Sliding-Window-O(n)-Solution

## 2398. Maximum Number of Robots Within Budget
https://leetcode.com/problems/maximum-number-of-robots-within-budget/

使用单调队列记录滑窗内最大值即可. 单调队列使用: 如果 new incoming value 比 队列 tail 大, 则去掉所有小于 new incoming value 的值, 使得 队列 保存区间最大值.

```java
public int maximumRobots(int[] chargeTimes, int[] runningCosts, long budget) {
    Deque<Integer> q = new ArrayDeque<>();
    int n = chargeTimes.length, left = 0, right = 0, max = 0;
    long sum = 0;
    while (right < n) {
        sum += runningCosts[right];
        while (!q.isEmpty() && chargeTimes[q.peekLast()] <= chargeTimes[right]) {        // 最大单调栈
            q.pollLast();
        }
        q.addLast(right);
        
        if (chargeTimes[q.peekFirst()] + (right - left + 1) * sum > budget) {
            if (q.peekFirst() == left)             // max value is at left index
                q.pollFirst();                                  // remove left one
            sum -= runningCosts[left];
            left++;
        }
        right++;
        max = Math.max(max, right-left);
    }
    return n - left;
}
```

## 239. Sliding Window Maximum
https://leetcode.com/problems/sliding-window-maximum/

使用单调队列记录区间最大值. (原理类似PQ). 

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length == 0)
            return new int[]{};
        
        Deque<Integer> q = new ArrayDeque<>();    
        int left = 0, right = 0, n = nums.length;
        int[] res = new int[n - k + 1];
        
        while (right < n) {
            int num = nums[right];
            while(!q.isEmpty() && nums[q.peekLast()] <= num) {      // 维护最大单调队列
                q.pollLast();
            }
            q.addLast(right);                                       
            
            if (right - q.peekFirst() >= k)  // 判断最大值是否越界
                q.pollFirst();
            
            if (right >= k - 1)             // 加入结果
                res[left++] = nums[q.peekFirst()];
            
            right++;
        }
        return res;
    }
}
```

## 155. Min Stack
https://leetcode.com/problems/min-stack/

单调最小栈实现. 使用栈, 只有当新值比当前最小值 (minStack.peek) 小, 才压入栈. 当 普通栈 的值和 单调栈栈顶的 值 一样(只比较值), pop 单调栈栈顶.

```java
class MinStack {
    Deque<Integer> s;
    Deque<Integer> minS;
    
    public MinStack() {
        s = new ArrayDeque<>();
        minS = new ArrayDeque<>();
    }
    
    public void push(int val) {
        s.push(val);
        if (minS.isEmpty() || val <= minS.peekFirst())  // 为空 || 新值比当前最小还小, 加入
            minS.addFirst(val);
    }
    
    public void pop() {
        if (minS.peekFirst().equals(s.peek()))               // pop element is at top of min Stack, only compare value
            minS.pollFirst();
        s.pop();
    }
    
    public int top() {
        return s.peek();
    }
    
    public int getMin() {
        return minS.peekFirst();
    }
}
```

## 503. Next Greater Element II
https://leetcode.com/problems/next-greater-element-ii/

环查找 + 单调栈. 环 = 遍历两次. 单调栈记录所有的 index, 如果栈顶位置的元素 小于 当前元素, 则 在栈顶位置记录最近的最大元素

```java
public int[] nextGreaterElements(int[] nums) {
    if (nums == null || nums.length == 0)
        return new int[0];
    
    int n = nums.length;
    Deque<Integer> stack = new ArrayDeque<>();      // 最大单调栈, 栈底最大
    int[] res = new int[n];
    Arrays.fill(res, -1);
    for (int i = 0; i < 2 * n; i++) {
        while (!stack.isEmpty() && nums[stack.peek() % n] < nums[i % n]) {  // 栈顶位置元素 < 当前元素
            res[stack.pop() % n] = nums[i % n]; 
        }
        stack.push(i);
    }
    return res;
}
```