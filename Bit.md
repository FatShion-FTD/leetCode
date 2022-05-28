# Bit Operation

^ XOR 操作: A^A=0,  A^0=A

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