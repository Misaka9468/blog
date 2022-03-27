# 题目

https://www.luogu.com.cn/problem/P2946

挑N个奶牛组队，队伍内数量>=1 <=N

每只奶牛的能力为整数，第 *i* 头奶牛的能力为R_i 。

要求队伍中所有奶牛能力总和是F的倍数，求方案数

# 思路

考虑选不选第i个奶牛，$dp[i][j]$表明前i个奶牛，总能力为j的倍数的方案数

那么$dp[i][j] = dp[i-1][j] + dp[i-1][(j + F - R_i)\%F]$

但是由于F的倍数是无限的，考虑取模，且$j-R_i$可能会小于0，但是依然有意义，所以就 $j + F - R_i$

# 代码

```cpp
#include<bits/stdc++.h>

using namespace std;
#define MAXN 2005
#define MOD 100000000
int N,F;
int dp[MAXN][1005];
int arr[MAXN];
int tmp;
int main(){
    scanf("%d%d",&N,&F);
    for(int i=1;i<=N;i++) {
	    scanf("%d",&tmp);
        arr[i] = tmp%F;
    }
    dp[0][0] = 1;
    for(int i=1;i<=N;i++)
        for(int j=0;j<=F;j++){
            dp[i][j] = (dp[i-1][j]+dp[i-1][(j+F-arr[i])%F])%MOD;
        }
    printf("%d\n",dp[N][F]);
    return 0;
}
```



