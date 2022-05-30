# Bit Operation

^ XOR 操作: A^A=0,  A^0=A

| OR 操作: 有 1 则 1

& AND 操作: 俩 1 则 1

~ 取反操作: 1 0 对调

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

使用 OR 和 int 记录 word 的每一位是否存在 char, 1 存在, 0 不存在

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