# 139. Word Break
https://leetcode.com/problems/word-break/

三种解法: 
1. 使用 Trie, 先构建 Trie, 在 Trie 中保持 isWord 来确认是否是单词, 
2. 使用 BFS, 将 s 按照 index 分割, 检查每个分割的字串是否是 dict set 中, 如果在, 则将结束的 index 加入 queue, 作为下一个分割的起点. 同时使用 visited, 确认每个 index 只被访问一次.
3. 使用 DP, i 从 1 到 s.length(), 作为 end index, 检查所有 j 从 0 到 i, 作为 start index, 看字串是否在 dict 中, 同时要确保 dp[j] 是 true,    
dp[j] && dict.contains(s.substring(j , i))

```java
// 解法2:
 public boolean wordBreak(String s, List<String> wordDict) {
     // 2. BFS, using index break, "leetcode" = 0-5-9, check dict.contains(s.substring(start, i))
     Set<String> dict = new HashSet<>(wordDict);
     Deque<Integer> q = new ArrayDeque<>();
     boolean[] visited = new boolean[s.length() + 1];
     
     q.offer(0);
     while(!q.isEmpty()){
         int start = q.poll();
         for(int i = start + 1; i <= s.length(); i++){
             if(visited[i])  continue;
             if(dict.contains(s.substring(start, i))){
                 if(i == s.length()) 
                     return true;
                 q.offer(i);
                 visited[i] = true;
             }
         }
     }
     
     return false;
 }

// 解法3:
 public boolean wordBreak(String s, List<String> wordDict) {
     Set<String> dict = new HashSet<>(wordDict);
     boolean[] dp = new boolean[s.length()+1];
     dp[0] = true;
     
     for(int i = 1; i <= s.length(); i++){
         for(int j = 0; j < i; j++){
             if(dp[j] && dict.contains(s.substring(j, i)))
                 dp[i] = true;
         }
     }
     
     return dp[s.length()];
 }
```
