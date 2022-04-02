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