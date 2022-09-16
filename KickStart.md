# Kick Start

## 模板
```java
import java.util.*;

class Solution {
    public Solution(){}

    public void solution(){
        return;
    }

    public static void main (String[] args) {
        Scanner sc = new Scanner(System.in);
        int size = 0;
        if (sc.hasNextInt())
            size = sc.nextInt();
        
        Solution slu = new Solution();

        for (int i = 1; i <= size; i++) {
            if (sc.hasNextInt())


            var res = slu.solution();
            System.out.println("Case #%d: %d", i, res);
        }
        sc.close();
    }
}
```