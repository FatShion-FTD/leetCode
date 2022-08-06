# PureMath 纯数问题
对于纯数学问题, 在于是否能够推理出来里面的数学关系, 和是否见过, 和本身的coding能力没啥关系

## Problems

780. Reaching Points: https://leetcode.com/problems/reaching-points/

常见面试题. 核心三点: 1. 使用 MOD 计算减少 中间状态的简单减法 过程, 即 ty = ty % tx;

2. 因为最后的状态可能是 (x, y+kx) 则有 ty = k * sx + sy 则需要确保 k是 整数, 同样使用 MOD, 确认模为0

3. 使用树思想, 把这个操作当作一个 tree, 因为 从 root 到 leaf 有无数条路, 使用**反向思想**, 从 leaf 到 root 则只存在一条路

```java
public boolean reachingPoints(int sx, int sy, int tx, int ty) {
    while(tx > sx && ty > sy){
        if(tx>ty){
            tx = tx % ty;   
        }else{
            ty = ty % tx;
        }
    }
    return  sx == tx && ty >= sy && (ty - sy) % tx == 0 ||
        sy == ty && tx >= sx && (tx - sx) % ty == 0;
}
```

2244. Minimum Rounds to Complete All Tasks: https://leetcode.com/problems/minimum-rounds-to-complete-all-tasks/

## 原理: 每个数字 都是 由 N个 2 和 3 组成的, 除了 1

如果 num 不能被 3 整除, 则 余数为1时, 取 3+1 = 4 -> 4 / 2 = 2; 余数为2时, 直接 +1. 则不能被 3 整除时, 直接 总数 +1 即可

```java
public int minimumRounds(int[] tasks) {
  Map<Integer, Integer> map = new HashMap<>();
  for(int num : tasks){
      map.put(num, map.getOrDefault(num, 0) + 1);
  }
  int count = 0;
  for(Map.Entry<Integer, Integer> entry : map.entrySet()){
      int total = entry.getValue();
      if(total == 1)  return -1;
      if(total % 3 == 0){
          count += total / 3;
      }else{
          count += (total / 3) + 1;
      }
  }
  return count;
}
```

## 343. Integer Break
https://leetcode.com/problems/integer-break/

根据数学关系, 贪心, 找 3, 留 4 即可
```java
public int integerBreak(int n) {
  if(n == 2)  return 1;
  if(n == 3)  return 2;
  
  // find 3 and 4
  int res = 1;
  while(n > 4){
      n -= 3;
      res *= 3;
  }
  return res * n;
}
```

## 2310. Sum of Numbers With Units Digit K
https://leetcode.com/problems/sum-of-numbers-with-units-digit-k/

跟据数学关系, 乘法的个位数可以作为验证 乘法是否合理的基础, 如 9 * 9 = 81, i * 9 = ?1, 遍历所有长度, 使用个位数进行验证

```java
public int minimumNumbers(int num, int k) {
  if(num == 0)    return 0;
  if(k == 0)  return num % 10 == 0 ? 1 : -1;
  for(int i = 1; i <= num / k; i++){   // from 1 to n * k == num
      if(num % 10 == (i * k) % 10)
          return i;
  }
  return -1;
}
```

462. Minimum Moves to Equal Array Elements II
https://leetcode.com/problems/minimum-moves-to-equal-array-elements-ii/

一般有2种中间值的找法: mean median, 此题选择使用median    
原因: 假设 AC 在两段, B 在中间, 如果选择median, AC都走到B, path = C - A; 如果选择 mean, 一旦 B 不在mean位置就需要再走一段, 则 path = C - A + |B - mean|;

```java
public int minMoves2(int[] nums) {
  Arrays.sort(nums);
  int res = 0, left = 0, right = nums.length - 1;
  while(left < right){
      res += nums[right--] - nums[left++];
  }
  return res;
}
```

## 453. Minimum Moves to Equal Array Elements
https://leetcode.com/problems/minimum-moves-to-equal-array-elements/

可以用迭代的办法解决, 但是O(NlogN), 从 sum 出发, 可以推出来计算公式, 如下

```java
public int minMoves(int[] nums) {
   // sum + m * (n-1) = x * n
   // x = min + m
   // sum + m(n-1) = (min+m)n
   // sum - m = min * n -> m = sum - min * n
   int sum = 0, min = nums[0], n = nums.length;
   for(int num : nums){
       sum += num;
       min = Math.min(min, num);
   }
   return sum - min * n;
}
```

## 858. Mirror Reflection (别他妈写你那逼模拟了)
https://leetcode.com/problems/mirror-reflection/

有意思的数学几何问题, 尝试扩展空间, 不要被一个 room 限制了想象力, 尝试扩展 room, 当反射触及upper bondary 又没有达到 corner, 扩展空间.     
数学关系: # of room * room length = # of reflect * q length (当是retancgle时可以调整)    
相等时, 如果 # of reflect 是 odd, 在左边, 返回 2;      
如果 # of reflect 是 even, 在右边, 跟据 # of room 返回 0 or 1;     

![https://s3-lc-upload.s3.amazonaws.com/users/motorix/image_1529877876.png](https://s3-lc-upload.s3.amazonaws.com/users/motorix/image_1529877876.png)

```java
public int mirrorReflection(int p, int q) {
   // need the step length, where the step = p/q
   // step wont be changed
   // if relect # is odd, must be on left hand
   // if reflect # is even, on right hand, see the room #, if odd, returnr 1
   int room = 1, qNum = 1;
   while (room * p != qNum * q) {
       qNum += 1;
       room = qNum * q / p; 
   }
   if (qNum % 2 == 0)
       return 2;
   if (room % 2 == 1)
       return 1;
   return 0;
}
```

## 458. Poor Pigs
https://leetcode.com/problems/poor-pigs/

经典msft面试题, 好玩的脑筋急转弯. 假设我们有 16个桐子, 2个猪, 3轮儿:    
1 2 3 4    
5 6 7 8    
9 10 11 12   
13 14 15 16     

一个猪头一轮喝一列, 另个猪头一轮喝一行, 通过交叉定位 和 排除法 就能定位.    
如 喝 3rd row的死了, 但是所有喝列的都没死, 则定位为第四列的 12    
综上: 一个猪头代表一个维度, (round + 1 ) ^ (# of pigs) = N

```java
public int poorPigs(int buckets, int minutesToDie, int minutesToTest) {
   if (minutesToTest / minutesToDie == 0 || buckets == 1)
       return 0;
   
   int res = 1, round = 1 + minutesToTest / minutesToDie, tot = (int)Math.pow(round, res);
   while (tot < buckets) {
       tot = (int)Math.pow(round, ++res);
   }
   
   return res;
}
```