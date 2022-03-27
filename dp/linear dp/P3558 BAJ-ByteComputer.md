# 题目

https://www.luogu.com.cn/problem/P3558

题目大意：

给定一个长度为 *n* 的只包含 −1,0,1 的数列 *a*，每次操作可以使 $a_i \leftarrow a_i+a_{i-1}$，求最少操作次数使得序列单调不降。如果不可能通过该操作使得序列单调不降，请输出 `BRAK`。

# 解题思路

**如何BRAK：**

0 -1 这种情况。   0前面没有1或-1，所以0只能为0，那么-1就不能被增加

**错误思路：**

dp[i]表示从a[0]到a[i]最少需要的步骤。

如果a[i-1] = -1 a[i] = 0 1 如何如何 dp[i] = dp[i-1] + xx

如果a[i-1] = 0 a[i] = x x 如何如何

【实际上是不对的，因为a[i-1]会变的】

举例：-1  1  0  0   如何变化？按这个错误思路，应该是dp[1] = 0 dp[2] = 1 dp[3] = dp[2]+1 = 2

实际上，只需要让a[1] = 0即可

**正确思路：**

  使用$dp[i][j] (j=0,1,2)$ 来表示从开始到 a[i] 的 值变化为j-1的时候，所需要的步骤。这样我们的dp就可以记录了a[i-1]的更新后的值信息。



转移方程见代码，根据a[i]的值与a[i-1]可能的值进行讨论。 

# 代码

```cpp
#include<iostream>
#include<algorithm>
#include<vector>
#include<cstring>
using namespace std;

#define MAXN 1000005
int n;
int a;
int dp[2][3];
int main(){
    scanf("%d",&n);
    scanf("%d",&a);
    dp[1][0] = dp[1][1] = dp[1][2] = 0x3f3f3f3f;
    dp[1][a+1] = 0; 
    for(int i=2;i<=n;i++){
        scanf("%d",&a);
        if(a==-1){
            dp[i&1][0] = dp[1-(i&1)][0];
            dp[i&1][1] = 0x3f3f3f3f;  // 从-1 变成 0 是不可能的，因为说明前面的是1，你要满足递增a[i]只能升级为1
            dp[i&1][2] = dp[1-(i&1)][2]+2;
        }
        else if(a==0){
            dp[i&1][0] = dp[1-(i&1)][0]+1;
            dp[i&1][1] = min(dp[1-(i&1)][1],dp[1-(i&1)][0]);
            dp[i&1][2] = dp[1-(i&1)][2]+1;
        }
        else{
            dp[i&1][0] = dp[1-(i&1)][0]+2;
            dp[i&1][1] = dp[1-(i&1)][0]+1;
            dp[i&1][2] = min(min(dp[1-(i&1)][0],dp[1-(i&1)][1]),dp[1-(i&1)][2]);
        }
    }
    int m = min(min(dp[n&1][0],dp[n&1][1]),dp[n&1][2]);
    if(m>=0x3f3f3f3f) printf("BRAK\n");
    else printf("%d",m);
    return 0;
}
```

