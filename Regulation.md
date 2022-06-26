# 正则表达式
[] 字符集合, 如: [abc], [+-]   
() 分组, 如: ()   
? 重复 0 ~ 1   
\+ 重复 1 ~ n   
\* 重复 0 ~ n    
. 任意字符    
很多转义字符前都需要两个反斜杠 \\\\  , 如:    
\\\\. 转移后的 .  
\\\\| 转义后的 |    
\\\d  数字0-9   

```java
public boolean isNumeric(char[] str) {
   if (str == null || str.length == 0)
       return false;
   return new String(str).matches("[+-]?\\d*(\\.\\d+)?([eE][+-]?\\d+)?");
}
```