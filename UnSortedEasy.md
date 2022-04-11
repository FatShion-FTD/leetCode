# 未分类的 EASY

Shift 2D Grid: https://leetcode.com/problems/shift-2d-grid/

思路: 拉平 grid 即可, O(1) 注意: 使用 % 进行非越界访问, 开始 index % 总长度 即到 0 的长度, 由此 % 出 row 和 col 即可

```java
public List<List<Integer>> shiftGrid(int[][] grid, int k) {
    List<List<Integer>> res = new ArrayList<>();
    if(grid.length == 0 || grid == null)    return res;
    
    int m = grid.length, n = grid[0].length;
    int index = m * n - (k % (m * n));
    
    for(int i = index; i < m * n + index; i++){ 
        int j = i % (m * n), r = j / n, c = j % n;
        if((i - index) % n == 0)    res.add(new ArrayList<>());
        res.get(res.size()-1).add(grid[r][c]);
    }
    return res;
}
```