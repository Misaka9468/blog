区间dp一般复杂度为$O(n^2)$

解题思路最关键的地方是要搞明白, $dp[i][j]$的含义是什么，包括是否为闭区间，等等

还有就是积累一些模型吧



**以及区间dp时的遍历方法，外层枚举r递增，内层枚举 l = r-1 逐渐递减. 最内侧再枚举 k = l.**

**记忆技巧是，我们dp肯定是从小区间到大区间推. 先枚举r递增，再从最小长度的区间也就是l=r-1慢慢变大.**

```cpp
    for(int r=2;r<=N;r++){
        for(int l=r-1;l>=1;l--){
            for(int k=l;k<r;k++){
                // such as 
                dp[l][r] = min(dp[l][r],dp[l][k]+dp[k+1][r]);
            }
        }
    }
```

