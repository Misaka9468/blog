# 一、P1514引水入城
## 1. 题目
https://www.luogu.com.cn/problem/P1514

为了使居民们都尽可能饮用到清澈的湖水，现在要在某些城市建造水利设施。水利设施有两种，分别为蓄水厂和输水站。蓄水厂的功能是利用水泵将湖泊中的水抽取到所在城市的蓄水池中。

因此，只有与湖泊毗邻的第1 行的城市可以建造蓄水厂。而输水站的功能则是通过输水管线利用高度落差，将湖水从高处向低处输送。故一座城市能建造输水站的前提，是存在比它海拔更高且拥有公共边的相邻城市，已经建有水利设施。由于第*N* 行的城市靠近沙漠，是该国的干旱区，所以要求其中的每座城市都建有水利设施。那么，这个要求能否满足呢？如果能，请计算最少建造几个蓄水厂；如果不能，求干旱区中不可能建有水利设施的城市数目。

## 2. 思路

靠近水源的城市，每个城市应该维护一个可以灌溉的下方区间。

对上方城市进行搜索，最后将区间覆盖。

我主要就是1.没想到区间覆盖怎么做。2.不知道怎么维护区间

启示：

* 原来可以记忆化搜索时，维护多个信息。如本题的_left和_right 
* 学会了怎么做区间覆盖.  $left[i][j]$  代表了点$arr[i][j]$所能灌溉到的沙漠层最左侧.

给我的启示是，这种先dfs再更新的递归做法是一个很好的模板。递归确保_left和\_right都为最新

## 3. 代码

```cpp
#include<bitset>
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<iostream>
#include<queue>
using namespace std;

#define MAXN 505

int height[MAXN][MAXN];
int visited[MAXN][MAXN];
int _left[MAXN][MAXN],_right[MAXN][MAXN];
int dir[4][2] = {{0,-1},{0,1},{1,0},{-1,0}};
int N,M,cnt0;

void dfs(int x,int y){
    visited[x][y]=1;
    int xx,yy;
    for(int i=0;i<4;i++){
        xx = x + dir[i][0];
        yy = y + dir[i][1];
        if(xx<1||xx>N||yy<1||yy>M) continue;
        if(height[x][y]<=height[xx][yy]) continue;
        if(!visited[xx][yy])
            dfs(xx,yy);
        _left[x][y]=min(_left[x][y],_left[xx][yy]);
        _right[x][y]=max(_right[x][y],_right[xx][yy]);
    }
}


int main(){
    scanf("%d%d",&N,&M);
    for(int i=1;i<=N;i++){
        for(int j=1;j<=M;j++){
            _left[i][j] = 0x3f3f3f3f;
            _right[i][j] = 0;
        }
    }
    for(int i=1;i<=M;i++)
        _left[N][i]=_right[N][i]=i;
    for(int i=1;i<=N;i++)
        for(int j=1;j<=M;j++)
            scanf("%d",&height[i][j]);
    for(int i=1;i<=M;i++){
        if(!visited[1][i])
            dfs(1,i);
    }
    // judge not
    for(int i=1;i<=M;i++){
        if(!visited[N][i]){
            cnt0++;
        }
    } 
    if(cnt0){
        printf("0\n%d",cnt0);
        return 0;
    }
    // judge ok
    int idx0=1,idx1=1;
    while(idx0<=M){
        for(int i=1;i<=M;i++){
            if(_left[1][i]<=idx0){
                idx1 = max(idx1,_right[1][i]);
            }
        }
        cnt0++;
        idx0 = idx1+1;
    }
    printf("1\n%d",cnt0);


}
```

# 二、P1535 Cow Travelling S

## 2.1 题目

https://www.luogu.com.cn/problem/P1535

【每秒都要走，不能原地停下】

N×M的草地。刚开始在一个位置，T秒后观测到在一个位置，在这些地图里还有树不能越过。现在你拿到了一张整块草地的地形图，其中 `.` 表示平坦的草地，`*` 表示挡路的树。你的任务是计算出，一头在 T*T* 秒内从 (*R*1,*C*1) 移动到(*R*2,*C*2) 的奶牛可能经过的路径有哪些。

【方案数】

## 2.2 思路

记忆化搜索，维护一个三维度信息。

剪枝一：奇偶性【假设我离目标只有1格，但是我还剩2s，那么我一定到达不了目的地点】

剪枝二：距离超出了，就不算了

$dp[i][j][t]$ 代表了处于 (i,j) 且剩余时间为t的情况时，能到达(R2,C2)的方案数

## 2.3 代码

```cpp
#include<utility>
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<iostream>
#include<queue>
using namespace std;

int N,M,T;
int R1,C1,R2,C2;
#define MAXN 105
char arr[MAXN][MAXN];
int dp[MAXN][MAXN][16];
int dir[4][2]={{0,-1},{0,1},{1,0},{-1,0}};


void dfs(int i,int j,int k){
    if(k==0&&!(i==R2&&j==C2)) return ;
    if(i==R2&&j==C2&&k==0){
        dp[i][j][k]=1;
        return ;
    }
    int x,y;
    for(int idx=0;idx<4;idx++){
        x = i+dir[idx][0];
        y = j+dir[idx][1];
        if(x<1||x>N||y<1||y>M) continue;
        if((abs(R2+C2-x-y)%2)!=((k-1)%2)) continue;
        if(abs(R2+C2-x-y)>k-1) continue;
        if(arr[x][y]=='*') continue;
        if(k==0) break;
        if(dp[x][y][k-1]!=0) 
            dp[i][j][k]+=dp[x][y][k-1];
        else{
            dfs(x,y,k-1);
            dp[i][j][k]+=dp[x][y][k-1];
        }
    }
}

int main(){
    scanf("%d%d%d",&N,&M,&T);
    for(int i=1;i<=N;i++)
        for(int j=1;j<=M;j++)
            cin>>arr[i][j];
    scanf("%d%d%d%d",&R1,&C1,&R2,&C2);
    memset(dp,0,sizeof(dp));
    dfs(R1,C1,T);
    printf("%d",dp[R1][C1][T]);
    return 0;
}
```