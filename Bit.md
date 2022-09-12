# Bit Operation

^ XOR 操作: A^A=0,  A^0=A

| OR 操作: 有 1 则 1

& AND 操作: 俩 1 则 1

~ 取反操作: 1 0 对调

\>> ? 右移 ? 位   

\>>> ? 无符号右移 ? 位, 无符号就是符号位也是移动, 如 -8 >>= 2, 变成正数

\<< ? 左移 ? 位

\<<< 无符号左移 ? 位

左移一位 = ori * 2

右移一位 = ori / 2

Java Bit规则: 正数就是普通的二进制, 负数就是取反加1的二进制     
如: -1 = 1b1111111111111111


## Bit 加减乘除
```java
// 加法
public int bitPlus(int a, int b){
    while(b){
        int temp = a;
        a = temp ^ b;  // ^ 处理同一位置
        b = (temp & b) << 1;   // & 处理加法进位
    }
    return a;
}

// 减法 = 加上相反数
public int bitSub(int a, int b){
    return bitPlus(neg(a), b);
}
// 跟据Java负数的定义(补码, 正负都是), 取反+1 获得一个数的负数: 
// 3 - 0b011, 1b101
public int neg(int a){
    return bitPlus(~a, 1);
}

// 乘法, 多次加法
public int bitProduct(int a, int b){
    int product = 0;
    int absA = abs(a), absB = abs(b);
    while(absB){
        product = bitPlus(product, absA);
        absB = bitSub(absB, 1);
    }
    return a ^ b) < 0 ? -product : product;
}

public int abs(int a){
    return a < 0 ? neg(a) : a;
}

// 除法, 多次减法
public int bitDiv(int dividend, int divisor){
    if(divisor == -1 && dividend == Integer.MIN_VALUE)  return Integer.MAX_VALUE;
    // 不计算 len 省时间
    int a = Math.abs(dividend), b = Math.abs(divisor), res = 0;
    for(int x = 31; x >= 0; x--){
        if((a>>>x) - b >= 0){   // 不使用 b << x 因为会为 0 
            res |= (1 << x);
            a -= b << x;
        }
    }
    return (dividend ^ divisor) < 0 ? -res : res;
}
```

## 进制转换
```java
public List<Boolean> trans(int n, int base) {
    List<Boolean> bits = new ArrayList<>();
    while (n >= 1) {
        bits.add(n % base == 1);
        n /= base;
    }
    return bits;
}
```
## Problems
Single Number: https://leetcode.com/problems/single-number/

直接使用 ^ , 把所有数 XOR 到一起, 剩下的就是 single number

```java
public int singleNumber(int[] nums) {
    int res = 0;
    for(int num : nums){
        res ^= num;
    }
    return res;
}
```
268. Missing Number: https://leetcode.com/problems/missing-number/

使用XOR遍历 [0,n] 和nums, 则退化为 single number

```java
public int missingNumber(int[] nums) {
    int res = 0;
    for(int i = 0; i < nums.length; i++){
        res ^= i ^ nums[i];
    }
    return res^nums.length;
}
```

318. Maximum Product of Word Lengths: https://leetcode.com/problems/maximum-product-of-word-lengths/

使用 OR 记录 word 的每一位是否存在 char, 1 存在, 0 不存在, 编码成 one-hot int 进行储存

temp |= 1 << (words[i].charAt(j) - 'a');

```java
public int maxProduct(String[] words) {
   int res = 0;
   int[] check = new int[words.length];    // store the bits of each word
   for(int i = 0; i < words.length; i++){
       int temp = 0;
       for(int j = 0; j < words[i].length(); j++){
           // j-th bit 1 if has char, j-th char - 'a', range: 0 - 26
           temp |= 1 << (words[i].charAt(j) - 'a');
       }
       check[i] = temp;
   }
   for(int i = 0; i < words.length; i++){
       for(int j = i + 1; j < words.length; j++){
           if((check[i] & check[j]) == 0){
               res = Math.max(res, words[i].length() * words[j].length());
           }
       }
   }
   
   return res;
}
```

1461. Check If a String Contains All Binary Codes of Size K: https://leetcode.com/problems/check-if-a-string-contains-all-binary-codes-of-size-k/

滑窗, 并使用 bit 计算 visited 存到 hashset, 检查 set 的大小是否符合要求

```java
public boolean hasAllCodes(String s, int k) {
   Set<Integer> hs = new HashSet<>();
   for(int i = 0; i <= s.length() - k; i++){
       int temp = 0;
       for(int j = 0; j < k; j++){
           temp |= (s.charAt(i + j) - '0') << (k-j);
       }
       if(hs.contains(temp))   continue;
       hs.add(temp);
   }
   
   return hs.size() == Math.pow(2, k);
}
```

## 2317. Maximum XOR After Operations
https://leetcode.com/problems/maximum-xor-after-operations/ 

问题主要在于操作: nums[i] AND (nums[i] ^ x) where x can be ANY number

1. 因为 AND 是最外围操作, 所以 不存在 0 变 1, 即 nums[i] 只减不增
2. nums[i] ^ x 允许将 nums[i] 变为 当前位数下的任意值
3. 此时寻找 XOR(nums) 最大, 变为在当前数组前提下, 保留各 bit 的 1, 即 res |= nums[i]

```java
class Solution {
     public int maximumXOR(int[] nums) {
        int res = 0;
        for(int num : nums) res |= num;
        return res;
    }
}
```

## 2397. Maximum Rows Covered by Columns
https://leetcode.com/problems/maximum-rows-covered-by-columns/

使用 bitmask 进行所有组合的遍历. 使用 二进制 number 记录选取情况, 1 代表选中. 遍历所有组合方式, 通过 1 - (1 << n), 比如 n = 3, 则存在 1 - 8 表示二进制组合.

```java
 public int maximumRows(int[][] matrix, int numSelect) {
     int m = matrix.length, n = matrix[0].length, res = 0;
     int maxVal = 1 << n;                        // traverse all the combination
     for (int mask = 1; mask < maxVal; mask++) {
         if (Integer.bitCount(mask) != numSelect)
             continue;
         
         int coverd = 0;
         for (int i = 0; i < m; i++) {
             boolean isCovered = true;
             for (int j = 0; j < n; j++) {
                 // mask >> j & 1, check the col at (mask >> j)
                 // if j-th col is not selected, and there is 1 at j-th col 
                 if ((mask >> j & 1) == 0 && matrix[i][j] == 1) {    
                     isCovered = false;
                     break;
                 }
             }
             if (isCovered) coverd++;
         }
         res = Math.max(res, coverd);
     }
     return res;
 }
```