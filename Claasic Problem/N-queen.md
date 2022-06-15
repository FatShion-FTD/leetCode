# 51 & 52. N-Queens I & II 
https://leetcode.com/problems/n-queens/

N皇后, 标准 DFS, 回溯算法, 注意 check 即可

DFS 搜 row, 每次在一个新的 row 下棋, for(int i = 0 : n) check (row, i) 位置即可

```java
int count = 0;
List<List<String>> res;
int n;

public List<List<String>> solveNQueens(int n) {
    if(n == 0)  return null;
    
    this.res = new ArrayList<>();
    char[][] temp = new char[n][n];
    this.n = n;
    
    // declare Board
    for(char[] t : temp){
        Arrays.fill(t, '.');
    }
    
    dfs(temp, 0);
    return res;
}

/***
Add new queens where not bother the first depth rows of queens
param:
    depth - depth of row
***/
private void dfs(char[][] temp, int row){
    if(row == n){
        // StrBuilder Board 2 Str
        List<String> toStrs = new ArrayList<>();
        for(int i = 0; i < n; i++){
            toStrs.add(new String(temp[i]));
        }
        res.add(toStrs);
        count++;
        return;
    }
    
    // only add Q at current row
    for(int i = 0; i < n; i++){
        temp[row][i] = 'Q';
        if(this.isValid(temp, row, i)){
            dfs(temp, row+1);
        }
        temp[row][i] = '.';
    }
}
/***
No Conflict on rows, but only on cols and diagals, careful while ignoring current point
***/
private boolean isValid(char[][] temp, int row, int col){
    // check cols
    // . . Q .
    // . . Q . 
    for(int i = 0; i < row; i++){
        if(temp[i][col] == 'Q')  return false;
    }
        
    // check diagonals 1
    // . . Q .
    // . . . Q x,y -> x-1 y-1
    for(int i = row-1, j = col-1; i >= 0 && j >= 0; i--, j--){
        if(temp[i][j] == 'Q')  return false;
    }
    
    // check diagonals 2
    // . Q . .
    // Q . . .  x,y -> x-1, y+1
    for(int i = row-1, j = col+1; i >= 0 && j < n; i--, j++){
        if(temp[i][j] == 'Q')  return false;
    }
    
    return true;
}
```