# String
作为 FINAL 储存的char[], 因为是FINAL 所以本身不能更改

## 常用类和函数

### StringBuilder & StringBuffer
功能相似, 但是

Trick: 直接使用 **"" + char**; 可以直接生成数组, 原理: ""是一个str, 这里一次string拼接, 缺点: string拼接非常cost

转换思路: **new StringBuilder().append(char).toString();**

### char[]
Char 可以直接遍历, 作为数字访问;

Char to Integer: **char - 's'** 's'是开始的char, 比如'0'和'a';




## Problems
Cells in a Range on an Excel Sheet: https://leetcode.com/problems/cells-in-a-range-on-an-excel-sheet/

```java
public List<String> cellsInRange(String s) {
    String[] strs = s.split(":");
    char[] start = strs[0].toCharArray();
    char[] end = strs[1].toCharArray();
    List<String> res = new ArrayList<>();
    
    for(char i = start[0]; i <= end[0]; i++){
        for(char j = start[1]; j <= end[1]; j++){
            res.add("" + i + j);
        }
    }
    
    return res;
}
```

Valid Parentheses: https://leetcode.com/problems/valid-parentheses/

```java
public boolean isValid(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    char[] charArr = s.toCharArray();
    
    for(char c : charArr){
        if(c == '(' || c == '{' || c == '['){
            stack.push(c);
        }else{
            if(stack.isEmpty()) return false;
            char pair = stack.pop();
            if(c == ')' && c != pair + 1) return false;
            if(c != ')' && c != pair + 2) return false;
        }
    }
    return stack.isEmpty();
}

public boolean isValid(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    char[] charArr = s.toCharArray();
    
    for(char c : charArr){
        if(c == '('){
            stack.push(')');
        }else if(c == '{'){
            stack.push('}');
        }else if(c == '['){
            stack.push(']');
        }else if(stack.isEmpty() || stack.pop() != c){
            return false;
        }
    }
    return stack.isEmpty();
}
```

Simplify Path: https://leetcode.com/problems/simplify-path/

使用skipWords来维持一个跳过词dict

```java
public String simplifyPath(String path) {
    String[] dirs = path.split("/");
    Deque<String> stack = new ArrayDeque<>();
    Set<String> skipWords = new HashSet<>(Arrays.asList("", ".", ".."));
    
    for(String dir : dirs){
        if(!stack.isEmpty() && dir.equals("..")) stack.pop();
        else if(!skipWords.contains(dir)) stack.push(dir);
    }
    
    StringBuilder sb = new StringBuilder();
    int size = stack.size();
    while(!stack.isEmpty()){
        sb.append("/").append(stack.pollLast());
    }
    return size == 0 ? "/" : sb.toString();
}
```

Minimum Remove to Make Valid Parentheses: https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses/

使用stack记录左括号, 遇到有效右括号, pop; 如果stack为空, 右括号无效, 加入remove; 最后把无效左括号加入remove, rebuild string

```java
public String minRemoveToMakeValid(String s) {
    Deque<Integer> stack = new ArrayDeque<>();
    Set<Integer> removes = new HashSet<>();
    char[] arr = s.toCharArray();
    for(int i = 0; i < arr.length; i++){
        if(arr[i] == '('){
            stack.push(i);
        }else{
            if(arr[i] == ')' && !stack.isEmpty()){
                stack.pop();
            }else if(arr[i] == ')' && stack.isEmpty()){
                removes.add(i);
            }
        }
    }
    
    removes.addAll(stack);
    
    StringBuilder sb = new StringBuilder();
    for(int i = 0; i < arr.length; i++){
        if(!removes.contains(i)){
            sb.append(arr[i]);
        }
    }
    
    return sb.toString();
}
```


String to Integer (atoi): https://leetcode.com/problems/string-to-integer-atoi/

先处理 空格, 正负号 最后算base. base的check: 判断是否临界(超过) Integer.MAX_VALUE / 10 再判断 如果 base == Integer.MAX_VALUE 时, 下一位是否触发越界 (主要看越界 和 边界条件)

```java
public int myAtoi(String s) {
    if(s.isEmpty()) return 0;
    char[] chars = s.toCharArray();
    int i = 0;
    boolean negSign = false;
    while( i < chars.length && chars[i] == ' ') i++;
    if (i >= s.length()) return 0;
    if(i < chars.length && chars[i] == '-' || chars[i] == '+'){
        negSign = chars[i++] == '-' ? true : false;
    }
    int base = 0;
    while(i < chars.length && chars[i] >= '0' && chars[i] <= '9'){
        
        if(base > Integer.MAX_VALUE / 10 || (base == Integer.MAX_VALUE / 10 && Integer.MAX_VALUE %10 < chars[i] - '0' )){
            return negSign ? Integer.MIN_VALUE : Integer.MAX_VALUE;
        }
        base = base * 10 + (chars[i++] - '0');
    }
    return negSign ? -base : base;
}
```

Longest Common Prefix: https://leetcode.com/problems/longest-common-prefix/

最长公共前缀 最长的前缀不会超过最短的 String 长度, 俩for 检测即可

```java
public String longestCommonPrefix(String[] strs) {
    StringBuilder sb = new StringBuilder();
    int minLen = Integer.MAX_VALUE;
    for(String s : strs)    minLen = Math.min(minLen, s.length());
    for(int i = 0; i < minLen; i++){
        char c = strs[0].charAt(i);
        boolean isCom = true;
        for(int j = 1; j < strs.length; j++){
            if(c != strs[j].charAt(i)){
                isCom = false;
                break;
            }
        }
        if(isCom){
            sb.append(c);
        }else break;   
    }
    return sb.toString();
}
```

Find Palindrome With Fixed Length: https://leetcode.com/problems/find-palindrome-with-fixed-length/

对于 palindrome 使用数字拆分的办法, 从 String 重新构建 数值, 把数字破成两半, 先算一半, 反过来再算一半

```java
public long[] kthPalindrome(int[] queries, int intLength) {
    long[] res = new long[queries.length];
    long half = (intLength + 1) / 2;
    long starter = (long)Math.pow(10, half - 1);    // palin的前一半 的 "base", 如 3位 的 base 是 10
    long totalPanli = (long)Math.pow(10, half) - starter;   // 结尾数字 100-10 = 90
    
    for(int i = 0; i < res.length; i++){
        if(queries[i] > totalPanli){
            res[i] = -1;
        }else{
            String firstHalf = (starter + queries[i] - 1) + "";   // base 是 10, 10 + 2 = 12, '12' + '1' = 121
            String secondHalf = (new StringBuilder(firstHalf)).reverse().toString();
            res[i] = Long.parseLong(firstHalf + secondHalf.substring(intLength % 2));   // 如果 intLength 是 odd, 则 不要 第一位
        }
    }
    return res;
}
```

Valid Palindrome II: https://leetcode.com/problems/valid-palindrome-ii/

相比一般的palin 多了一层嵌套check就是了

```java
public boolean validPalindrome(String s) {
    int lo = 0, hi = s.length() - 1;
    while(hi > lo){
        if(s.charAt(lo) == s.charAt(hi)){
            lo++;
            hi--;
        }else{
            return isPalin(s, lo + 1, hi) || isPalin(s, lo, hi - 1);
        }
    }
    return true;
}
private boolean isPalin(String s, int start, int end){
    int lo = start, hi = end;
    while(hi>lo){
        if(s.charAt(lo)!=s.charAt(hi))  return false;
        hi--;   lo++;
    }
    return true;
}
```
2243. Calculate Digit Sum of a String: https://leetcode.com/problems/calculate-digit-sum-of-a-string/

简单迭代, 每次访问 k 个元素, 加入新的 StringBuilder 即可

```java
public String digitSum(String s, int k) {
    StringBuilder sb = new StringBuilder(s);
    while(sb.length() > k){
        int i = 0;
        StringBuilder temp = new StringBuilder();
        char[] chars = sb.toString().toCharArray();
        while(i<sb.length()){
            int start = i, sum = 0;
            i = Math.min(chars.length, i+k);
            for(int j = start; j < i; j++){
                sum += chars[j] - '0';
            }
            temp.append(sum);
        }
        sb = temp;
    }
    return sb.toString();
}
```

## 1048. Longest String Chain
https://leetcode.com/problems/longest-string-chain/

先根据 str 的长度排序, 单增; 然后从短到长对每个 str 进行删除字符处理, 查询map是否存在对应的 deleted str, 如果有且新 chain 长度更长, 更新; 每个 str 结束, 比较结果

```java
public int longestStrChain(String[] strs) {
  // only a-z
  // predecessor A: add one any letter to A, A == B
  // from shortest to longer, build String chain by using map
  
  int res = 0;
  Arrays.sort(strs, (o1, o2) -> o1.length() - o2.length());  // ascending order 
  Map<String, Integer> map = new HashMap<>();
  
  for(String s : strs){
      map.put(s, 1);
      for(int i = 0; i < s.length(); i++){
          StringBuilder sb = new StringBuilder(s);
          String nextStr = sb.deleteCharAt(i).toString();
          if(map.containsKey(nextStr) && map.get(nextStr) + 1 > map.get(s))
              map.put(s, map.get(nextStr) + 1);
      }
      res = Math.max(res, map.get(s));
  }
  return res;
}
```

## 527. Word Abbreviation
https://leetcode.com/problems/word-abbreviation/

首先实现构建Abbr函数, 使用prefix记录每个对应的 str prefix长度, 每次逐个遍历, 使用set去重即可 

```java
public List<String> wordsAbbreviation(List<String> words) {
  int n = words.size();
  int[] prefix = new int[n];
  String[] strs = new String[n];
  Arrays.fill(prefix, 1);
  for(int i = 0; i < n; i++)  strs[i] = buildAbbr(words.get(i), prefix[i]);
  
  for(int i = 0; i < n; i++){
      while(true){
          Set<Integer> set = new HashSet<>();
          for(int j = i+1; j < n; j++){
              if(strs[i].equals(strs[j])) set.add(j);
          }
          if(set.isEmpty())   break;
          set.add(i);
          for(int k : set){
              strs[k] = buildAbbr(words.get(k), ++prefix[k]);
          }
      }
  }
  return Arrays.asList(strs);
}
// 0-th char + length() - 2 + length()-1-th char
// 1 char abbreviated not valid
// 0 - i th char + length() - i - 1 + length()-1-th char
private String buildAbbr(String s, int k){
  if(k >= s.length() - 2) return s;
  StringBuilder sb = new StringBuilder();
  sb.append(s.substring(0, k));
  sb.append(s.length() - k - 1);
  sb.append(s.charAt(s.length() - 1));
  return sb.toString();
}
```

## 395. Longest Substring with At Least K Repeating Characters
https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/

1. 用 freq < k 的 invalid char 作为隔断
2. 在第一次 invalid 时, 递归进入 subString(left, right); 其余时候, right += 1

```java
public int longestSubstring(String s, int k) {
  char[] arr = s.toCharArray();
  
  int[] freq = new int[26];
  for(int i = 0; i < arr.length; i++){
      freq[arr[i] - 'a'] += 1;
  }
  
  boolean isComp = true;
  for(int i = 0; i < arr.length; i++){
      int f = freq[arr[i] - 'a'];
      if( f > 0 && f < k){
          isComp = false;
          break;
      }
  }
  if(isComp)  return s.length();
  
  int res = 0, left = 0, right = 0;
  while(right < arr.length){
      if(freq[arr[right] - 'a'] < k){
          res = Math.max(res, longestSubstring(s.substring(left, right), k));
          left = right + 1;
      }
      right++;
  }
  res = Math.max(res, longestSubstring(s.substring(left), k));
  return res;
}
```

# Rolling Hash 滚动 Hash 
滚动hash算法, 首先是 Hash, 对一个 subarray 进行编码, 编辑成一个Integer, 用于判断 Collision; 在一般 Hash 的基础上, rolling hash 能够使用之前的 hash 信息:     
Sub1 = num[i] * 10^2 + num[i+1] * 10^1 + num[i+2] * 10^0     
Sub2 = (Sub1 - num[i] * 10^2) * 10 + num[i+3] * 10^0    
collision 是不能避免的, 上述例子, base 是 10, base 取值要注意

## 187. Repeated DNA Sequences
https://leetcode.com/problems/repeated-dna-sequences/

```java
public List<String> findRepeatedDnaSequences(String s) {
   int cur = 0, shift = (int)Math.pow(7, 9);
   Set<String> res = new HashSet<>();
   Set<Integer> set = new HashSet<>();
   Map<Character, Integer> map = new HashMap<>();
   map.put('A', 0);    map.put('C', 1);    map.put('G', 2);    map.put('T', 3);

   for(int i = 0; i < s.length(); i++){
      if(i > 9)   
          cur -= shift * map.get(s.charAt(i - 10));
      cur = cur * 7 + map.get(s.charAt(i));
      if(i > 8 && !set.add(cur)){
          res.add(s.substring(i-9,i+1));
      }  
   }

   return new ArrayList<>(res);
}
```


# String 子串, last occur
## 2262. Total Appeal of A String
https://leetcode.com/problems/total-appeal-of-a-string/



```java
 public long appealSum(String s) {
     // find the last appear and last appear of special char
     // all substring start after the last appear (i - last[c-'a']: # of start position)
     // end after new appear contains this char (n - i: # of end positoin)
     // (i - last[c-'a']) * (n - i)
     // fill last with -1, so that when char first counted, at least 1 start position
     if (s == null || s.length() == 0)
         return 0l;
     
     char[] arr = s.toCharArray();
     long res = 0l;
     int[] lastOccur = new int[26];
     Arrays.fill(lastOccur, -1);
     for (int i = 0; i < arr.length; i++) {
         res += (long) (i - lastOccur[arr[i] - 'a']) * (arr.length - i);
         lastOccur[arr[i] - 'a'] = i;
     }
     return res;
 }
```

## 179. Largest Number
https://leetcode.com/problems/largest-number/

String 组合的大小比较, 求两个String组合的最大情况. 最简单的 String 比较器: o1 + o2 和 o2 + o1 再比较二者ascii大小即可, 其实也可以用指针, 但是很麻烦, 略过

```java
 public String largestNumber(int[] nums) {
     if (nums == null || nums.length == 0)
         return "";
     
     StringBuilder sb = new StringBuilder();
     int n = nums.length;
     String[] strs = new String[n];
     
     for (int i = 0; i < n; i++) {
         strs[i] = String.valueOf(nums[i]);
     }
     
     Arrays.sort(strs, new Comparator<String>(){
           @Override
           public int compare(String o1, String o2){
               String str1 = o1 + o2;
               String str2 = o2 + o1;
               return str1.compareTo(str2);
           }
       });
     
     for (int i = n - 1; i >= 0; i--) {
         if (i == n - 1 && strs[i].equals("0")) 
             return "0";
         sb.append(strs[i]);
     }
     
     return sb.toString();
 }
```