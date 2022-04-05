# 题目

给定一个1*n的地图，在里面玩2048，每次可以合并相邻两个（数值范围1-40），问序列中出现的最大数字的值最大是多少。注意合并后的数值并非加倍而是+1，例如2与2合并后的数值为3。

https://www.luogu.com.cn/problem/P3146



# 解题思路

* 本题的特点在于，合并不是无条件的。

* 若$dp[i][j]$ 为$a[i]$到$a[j]$中出现的最大值？那么你会发现找不到合适的转移方程
* 若$dp[i][j]$ 为 必须合并$a[i]$或$a[j]$的最大值，那么在合并相邻区间时也没法做
* 根据合并要求出发，定义$dp[i][j]$为 从$a[i]$到$a[j]$ 能合并成一个值时的最大值。
  * 考虑一个区间，若其最终能2048成1个数字，那它前一步一定是两个相同的值合并。
  * 因此若$dp[i][k]==dp[k+1][j]$时，$dp[i][j] = it+1$ 否则为0表示没办法合并成1个数字
* 并且，我们最终的数列并非都能合成为1个数字，所以不能认为$dp[1][n]$就为答案，其很有可能就是0. 真正的答案存在任意的 $dp[i][j]$ 中，所以要实时维护。

# 代码

```cpp
#include<iostream>
#include<cstring>
#include<algorithm>

using namespace std;

#define MAXN 250

int dp[MAXN][MAXN];

int arr[MAXN];
int maxn=-1;
int N;

int main(){
    scanf("%d",&N);
    for(int i=1;i<=N;i++){
        scanf("%d",&arr[i]);
        dp[i][i] = arr[i];
    }

    for(int r=2;r<=N;r++)
        for(int l=r-1;l>=1;l--)
            for(int k=l;k<r;k++){
                if(dp[l][k]==dp[k+1][r]&&dp[l][k]){
                    dp[l][r] = max(dp[l][r],dp[l][k]+1);
                    maxn = max(maxn,dp[l][k]+1);
                }
            }
    printf("%d",maxn);
    return 0;
}
```

