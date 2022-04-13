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