# 0/1背包

每件物品只有选或不选两种可能

i 代表前i个物品，j代表weight(限制)

注意要遍历所有i j

**状态转移方程：**

$dp[i][j] = max(dp[i-1][j],v[i]+dp[i-1][j-w[i]])$



0/1背包的滚动写法：

```
for i in 1->n:
	for j in m->1: // 倒序，避免重复选取
		dp[j] = max(dp[j],dp[j-w[i]]+v[i])
```



# 完全背包

每件物品都可以选无数次

完全背包的滚动写法：

```
for i in 1->n:
	for j in 1->m: // 正序，可重复选取
		dp[j] = max(dp[j],dp[j-w[i]]+v[i])
```



# 多重背包

【与完全背包的区别是，每个物品是有限件】

有 N 种物品和一个容量是 V 的背包。

第 i 种物品最多有 $s_i$ 件，每件体积是 $w_i$，价值是 $v_i$。

求解将哪些物品装入背包，可使物品体积总和不超过背包容量，且价值总和最大。



**在0/1背包的基础上多加一层循环k，k的s[i]表示该物品最多有s[i]件**

**由于是基于0/1背包的，且使用滚动数组来完成，所以对于weight来讲是倒序遍历的**

**遍历k是正向的，因为k越大，dp[idx]的idx越小，也就保证了选1件更新不会影响选2件更新**

```cpp
	for(int i=1;i<=n;i++){
		for(int j=m;j>=w[i];j--){ // 在0/1背包基础上做的一维，所以weight倒序
			for(int k=1;k<=s[i] && j>=k*w[i];k++) 
				dp[j]=max(dp[j],dp[j-k*w[i]]+v[i]*k);
		} 
	}
```



# 多维费用背包

是指一个物品拥有多种weight。

比如，这件事既要花费时间也要花费金钱，但是我的时间与金钱都是有限的。

做法是将背包扩充维度。

[P1855 榨取kkksc03](https://www.luogu.com.cn/problem/P1855)

本题如果想降一维度，那么要保证时间、金钱即j k都要倒着遍历

```cpp
#include<iostream>
#include<algorithm>
#include<vector>
#include<cstring>
using namespace std;

int n,M,T;

int dp[105][205][205];
int m[105],t[105];
int main(){
    scanf("%d%d%d",&n,&M,&T);
    for(int i=1;i<=n;i++){
        scanf("%d%d",&m[i],&t[i]);
    }
    for(int i=1;i<=n;i++){
        for(int j=1;j<=M;j++){
            for(int k=1;k<=T;k++){
                if(j>=m[i]&&k>=t[i]){
                    dp[i][j][k] = max(dp[i-1][j-m[i]][k-t[i]]+1,dp[i-1][j][k]);
                }
                else dp[i][j][k] = dp[i-1][j][k];
            }
        }
    }
    printf("%d\n",dp[n][M][T]);
    return 0;
}
```



# 分组背包

有 N 组物品和一个容量是 V 的背包。

每组物品有若干个，**同一组内的物品最多只能选一个**。

每件物品的体积是 $w_{ij}$，价值是$v_{ij}$，其中 i 是组号，j 是组内编号。

求解将哪些物品装入背包，可使物品总体积不超过背包容量，且总价值最大。

**解题思路：**

一层遍历组数，二层遍历各重量，三层遍历当前组的所有物品，使用0/1背包计算。二层遍历结束后还要更新一下，表示该组的物品都不选。

[P1757 通天分组](https://www.luogu.com.cn/problem/P1757)

```cpp
#include<iostream>
#include<algorithm>
#include<vector>
#include<cstring>
using namespace std;

#define MAXN 1005
int m,n;
int ta,tb,tc;
int maxc;
int cnt[1005];
int w[MAXN][MAXN];
int v[MAXN][MAXN];
int dp[MAXN][MAXN];
int main(){
    scanf("%d%d",&m,&n);
    for(int i=1;i<=n;i++){
        scanf("%d%d%d",&ta,&tb,&tc);
        cnt[tc]++;
        maxc = max(tc,maxc);
        w[tc][cnt[tc]] = ta;
        v[tc][cnt[tc]] = tb;
    }
    for(int i=1;i<=maxc;i++){  // every group
        for(int j=1;j<=m;j++){ // every weight  
            for(int k=1;k<=cnt[i];k++){ // every sth in group i
                if(j>=w[i][k]) 
                    dp[i][j] = max(dp[i][j],dp[i-1][j-w[i][k]]+v[i][k]);
                dp[i][j] = max(dp[i][j],dp[i-1][j]);
            }
        }
    }
    printf("%d\n",dp[maxc][m]);
    return 0;
}
```

# 依赖背包

如果要选择一个物品，必须要选择另一件物品。

求解将哪些物品装入背包，可使物品总体积不超过背包容量，且总价值最大。

如【金明的预算方案】

主要思路是，将主-附看作一个组，对该组内进行分类讨论，可以看成是特殊的分组背包。分组背包中各分组是独立的，所以用变量遍历；依赖背包的组有主从关系，所以需要列出方案数分别讨论

