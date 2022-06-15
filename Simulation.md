# Simulation 模拟问题


289. Game of Life: https://leetcode.com/problems/game-of-life/

GOL问题: 俩操作: 对于1, 1 -> 0 if n(1) < 2 || n > 3; 对于0, 0 -> 1 if n(1) == 3 没了

注意一个trick: 在 board[i][j] == 1时, count 需要 +1

```java
class Solution {
    int m;
    int n;
    
    public void gameOfLife(int[][] board) {
        if(board.length == 0 || board == null)  return;
        
        m = board.length;
        n = board[0].length;
        int[][] next = new int[m][n];
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                next[i][j] = count(board, i , j);
            }
        }
        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                board[i][j] = next[i][j];
            }
        }
        return;
    }
    
    private int count(int[][] board, int row, int col){
        int count = 0;
        for(int i = row-1; i <= row+1; i++){
            for(int j = col-1; j <= col+1; j++){
                if(i >= 0 && i < m && j >= 0 && j < n && board[i][j] == 1)  count+=1;
            }
        }
        
        if(board[row][col] == 0){
            return count == 3 ? 1 : 0;
        }
        return count == 3 || count == 4 ? 1 : 0;
    }
}
```

59. Spiral Matrix II: https://leetcode.com/problems/spiral-matrix-ii/

记录 row 和 col 的 start 和 end, 依次遍历即可

```java
public int[][] generateMatrix(int n) {
   int[][] m = new int[n][n];
   int val = 1;
   int rowEnd = n-1, colEnd = n-1, row = 0, col = 0;
   
   while(row<=rowEnd && col<=colEnd){
       for(int i = col; i <= colEnd; i++){
           m[row][i] = val++;
       }
       row++;
       
       for(int i = row; i <= rowEnd; i++){
           m[i][colEnd] = val++;
       }
       colEnd--;
       
       for(int i = colEnd; i >= col; i--){
           m[rowEnd][i] = val++;
       }
       rowEnd--;
       
       for(int i = rowEnd; i >= row; i--){
           m[i][col] = val++;
       }
       col++;
   }
   return m;
}
```

## 43. Multiply Strings
https://leetcode.com/problems/multiply-strings/

![https://drscdn.500px.org/photo/130178585/m%3D2048/300d71f784f679d5e70fadda8ad7d68f](https://drscdn.500px.org/photo/130178585/m%3D2048/300d71f784f679d5e70fadda8ad7d68f)

乘法的 位置关系, 从右往左访问, 同时从右往左读:

 当 index i 和 index j 时, base 位置在 i+j, carry 位置在 i+j+1. base位置因为加上了原来的值来计算进位, 所以直接赋值, carry位置则需要累加

```java
public String multiply(String num1, String num2) {
    char[] arr1 = num1.toCharArray();
    char[] arr2 = num2.toCharArray();
    
    int[] res = new int[arr1.length + arr2.length];
    for(int i = arr1.length - 1; i >= 0; i--){
        for(int j = arr2.length - 1; j >= 0; j--){
            int mul = (arr1[i] - '0') * (arr2[j] - '0');
            int carr = i+j, base = i+j+1;
            int sum = mul + res[base];
            res[carr] += sum / 10;
            res[base] = sum % 10;
        }
    }
    StringBuilder sb = new StringBuilder();
    for(int i : res)    if(!(sb.length() == 0 && i==0)) sb.append(i);
    return sb.length() == 0 ? "0": sb.toString();
}
```