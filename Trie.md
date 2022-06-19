# Trie 树
Trie 主要有 前缀树 和 后缀树, 常用于各种 String 中的 token matching    
能够将 Map 的 O(n) 时间复杂度 简化到 O(L), L 是 最长String.length()   
使用 Trie 树有技巧, 如对字符串进行 suffix + '#' + str / suffix + '#' + prefix 的拼接, 实现更好的访问   
标准化结构和函数:
```java
public class Trie{
    int count;  // 以当前字符组合 为停止 的单词的数量
    int prefix; // 以当前字符组合 为前缀 的单词数量
    Trie[26] next = new Trie[26];
    public Trie(){
        count = 0;
        prefix = 0;
    }
}

public static void insert(Trie root, String s){
    if(root == null || s == null)   return;
    char[] c = s.toCharArray();
    for(int i = 0; i < s.length(); i++){
        if(root.next[c[i] - 'a'] == null)   
            root.next[c[i] - 'a'] = new Trie();
        root = root.next[c[i] - 'a'];
        root.prefix++; // 因为root不存信息 + prefix 表示之前的组合
    }
    root.count++;   // str end, 字符数 +1
}

public static Trie search(Trie root, String s){
    if(root == null || s == null) return null;
    char[] c = s.toCharArray();
    for(int i = 0; i < s.length(); i++){
        if(root.next[c[i] - 'a'] == null) return null;
        root = root.next[c[i] - 'a'];
    }
    return root;
}
```


## 208. Implement Trie (Prefix Tree)
https://leetcode.com/problems/implement-trie-prefix-tree/

```java
class Trie {
    
    class TrieNode{
        TrieNode[] next;
        boolean complete;
        public TrieNode(){
            next = new TrieNode[26];
            complete = false;
        }
    }
    
    TrieNode root;
    
    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode node = root;
        char[] c = word.toCharArray();
        for(int i = 0; i < c.length; i++){
            if(node.next[c[i] - 'a'] == null)
                node.next[c[i] - 'a'] = new TrieNode();
            node = node.next[c[i] - 'a'];
        }
        node.complete = true;
    }
    
    public boolean search(String word) {
        TrieNode node = root;
        char[] c = word.toCharArray();
        for(int i = 0; i < c.length; i++){
            if(node.next[c[i] - 'a'] == null)
                return false;
            node = node.next[c[i] - 'a'];
        }
        return node.complete;
    }
    
    public boolean startsWith(String prefix) {
        TrieNode node = root;
        char[] c = prefix.toCharArray();
        for(int i = 0; i < c.length; i++){
            if(node.next[c[i] - 'a'] == null)
                return false;
            node = node.next[c[i] - 'a'];
        }
        return node != null;
    }
}
```

527. Word Abbreviation
https://leetcode.com/problems/word-abbreviation/





```java


```

## 745. Prefix and Suffix Search
https://leetcode.com/problems/prefix-and-suffix-search/

标准化使用 Double Trie, 记忆 indexs, 同时使用 HashMap 加速 input, 避免 duplicate input 造成的输入超时

```java
class WordFilter {
    
    // Trie[26]
    class Trie{
        Trie[] next;
        Set<Integer> idxs;
        public Trie(){
            next = new Trie[26]; // character - 'a'
            idxs = new HashSet<>();    // index in strs
        }
    }

    Trie prefixTrie = new Trie();
    Trie suffixTrie = new Trie();
    Map<String, Integer> map = new HashMap<>();
        
    public WordFilter(String[] strs) {
        for(int i = 0; i< strs.length; i++) map.put(strs[i], i);
        
        for(String str : map.keySet()){
            // build prefix
            insert(prefixTrie, str, map.get(str), true);
            // build suffix
            insert(suffixTrie, str, map.get(str), false);
        }
        
    }
    
    public void insert(Trie root, String str, int index, boolean isPrefix){
        if(root == null || str == null) return;
        int i = isPrefix ? 0 : str.length() - 1;
        char[] c = str.toCharArray();
        if(isPrefix){
            while(i < str.length()){
                if(root.next[c[i] - 'a'] == null)
                    root.next[c[i] - 'a'] = new Trie();
                root = root.next[c[i] - 'a'];
                root.idxs.add(index);
                i++;
            }
        }else{
            while(i >= 0){
                if(root.next[c[i] - 'a'] == null)
                    root.next[c[i] - 'a'] = new Trie();
                root = root.next[c[i] - 'a'];
                root.idxs.add(index);
                i--;
            }
        }
    }
    
    public Trie search(Trie root, String str, boolean isPrefix){
        if(root == null || str == null) return null;
        char[] c = str.toCharArray();
        if(isPrefix){
            for(int i = 0; i < str.length(); i++){
                if(root.next[c[i] - 'a'] == null)   return null;
                root = root.next[c[i] - 'a'];
            }
        }else{
            for(int i = c.length - 1; i >= 0; i--){
                if(root.next[c[i] - 'a'] == null)   return null;
                root = root.next[c[i] - 'a'];
            }
        }
        return root;
    }
    
    public int f(String prefix, String suffix) {
        Trie prefixRes = search(prefixTrie, prefix, true);
        Trie suffixRes = search(suffixTrie, suffix, false);
        if(prefixRes == null || suffixRes == null)  return -1;
        
        int res = -1;
        Set<Integer> prefixSet = prefixRes.idxs;
        Set<Integer> suffixSet = suffixRes.idxs;
        for(Integer idx : prefixSet){
            if(suffixSet.contains(idx)) res = Math.max(res, idx);
        }
        return res;
    }
}
```