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