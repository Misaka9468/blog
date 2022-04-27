# 1. BFS侵蚀与逆向思维

## 1.1 题目

https://www.luogu.com.cn/problem/P1162

题目大意： 将二维矩阵中被1包裹起来的区域“值为0”变为“值为2”

## 1.2 思路

* 要能看出，如果一块0区域在bfs的时候能碰到边界，则不为目标区域。

* **逆向思维：从边界开始侵蚀为0，边界侵蚀不到的0群一定为孤岛**

## 1.3 代码

```cpp
#include<iostream>
#include<cstring>
#include<algorithm>
#include<queue>
#include<utility>

using namespace std;
#define MAXN 35
int arr[MAXN][MAXN];
int mark[MAXN][MAXN];
int n;


void bfs(int posx,int posy){
    queue<pair<int,int>> q;
    q.push(make_pair(posx,posy));
    while(!q.empty()){
        int x = q.front().first;
        int y = q.front().second;
        q.pop();
        if(x<1||x>n||y<1||y>n) {
            mark[x][y]=1;
            continue;
        }
        mark[x][y] = 1;
        if(!mark[x-1][y]) q.push(make_pair(x-1,y));
        if(!mark[x+1][y]) q.push(make_pair(x+1,y));
        if(!mark[x][y-1]) q.push(make_pair(x,y-1));
        if(!mark[x][y+1]) q.push(make_pair(x,y+1));
    }
}

int main(){
    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            scanf("%d",&arr[i][j]);
        }
    }
    memcpy(mark,arr,sizeof(arr));
    for(int i=1;i<=n;i++){
        if(!mark[1][i]) bfs(1,i);
        if(!mark[i][1]) bfs(i,1);
        if(!mark[i][n]) bfs(i,n);
        if(!mark[n][i]) bfs(n,i);
    }
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            if(arr[i][j]) printf("1 ");
            else if(mark[i][j]) printf("0 ");
            else printf("2 ");
        }
        printf("\n");
    }
    return 0;
}
```

# 2. bfs的重复入队

## 2.1 题目

https://www.luogu.com.cn/problem/P1443

有一个 *n*×*m* 的棋盘，在某个点 (*x*,*y*) 上有一个马，要求你计算出马到达棋盘上任意一个点最少要走几步。

## 2.2 出错点

* 在将马的八个方向加入到队列中时，由于我们一次是清空了所有队列(保证分层)，所以会导致某个点重复进队列。

* 解决方法：在push的同时标记该点，使其不会被重复选取

* 或者不用分层的方法解决，用vis+结构体记录该点的step

## 2.3 代码

```cpp
#include<iostream>
#include<cstring>
#include<algorithm>
#include<queue>
#include<utility>

using namespace std;
#define MAXN 403
int n,m,x,y;

int steps[MAXN][MAXN];


void bfs(int posx,int posy){
    int k=1;
    queue<pair<int,int>> q;
    q.push(make_pair(posx,posy));
    while(!q.empty()){
        int size = q.size();
        while(size--){
            int i = q.front().first;
            int j = q.front().second;
            q.pop();
            steps[i][j]=k;
            // push时同时进行标记，不然可能会重复入队
            if(i+1<=n&&j+2<=m&&!steps[i+1][j+2]) 
                q.push(make_pair(i+1,j+2)),steps[i+1][j+2]=k+1;
            if(i+2<=n&&j+1<=m&&!steps[i+2][j+1]) 
                q.push(make_pair(i+2,j+1)),steps[i+2][j+1]=k+1;
            if(i+2<=n&&j-1>=1&&!steps[i+2][j-1]) 
                q.push(make_pair(i+2,j-1)),steps[i+2][j-1]=k+1;
            if(i+1<=n&&j-2>=1&&!steps[i+1][j-2]) 
                q.push(make_pair(i+1,j-2)),steps[i+1][j-2]=k+1;
            if(i-1>=1&&j-2>=1&&!steps[i-1][j-2]) 
                q.push(make_pair(i-1,j-2)),steps[i-1][j-2]=k+1;
            if(i-2>=1&&j-1>=1&&!steps[i-2][j-1]) 
                q.push(make_pair(i-2,j-1)),steps[i-2][j-1]=k+1;
            if(i-2>=1&&j+1<=m&&!steps[i-2][j+1]) 
                q.push(make_pair(i-2,j+1)),steps[i-2][j+1]=k+1;
            if(i-1>=1&&j+2<=m&&!steps[i-1][j+2]) 
                q.push(make_pair(i-1,j+2)),steps[i-1][j+2]=k+1;
        }
        k++;
    }
}
int main(){
    scanf("%d%d%d%d",&n,&m,&x,&y);
    bfs(x,y);
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            printf("%-5d",steps[i][j]-1);
        }
        printf("\n");
    }
    return 0;
}


```



# 3 类似习题总结

## 3.1 LC417 太平洋大西洋水流

逆向思路，从边开始往内侵蚀，所有边能访问到的内部点都可以

```cpp
class Solution {
public:

    bool visPa[205][205];
    bool visAt[205][205];
    // flag=0 -> Pacific 
    // flag=1 -> Atlantic
    void bfs(int ri,int ci,vector<vector<int>>& heights,bool flag){
        int n = heights[0].size();
        int m = heights.size();
        queue<pair<int,int>> q;
        q.push(make_pair(ri,ci));
        while(!q.empty()){
            int r,c;
            r = q.front().first;
            c = q.front().second;
            q.pop();
            if(flag==0){
                visPa[r][c] = 1;
                if(r>0&&visPa[r-1][c]==0&&heights[r-1][c]>=heights[r][c]){
                    visPa[r-1][c] = 1;
                    q.push(make_pair(r-1,c));
                }
                if(r<m-1&&visPa[r+1][c]==0&&heights[r+1][c]>=heights[r][c]){
                    visPa[r+1][c] = 1;
                    q.push(make_pair(r+1,c));
                }
                if(c>0&&visPa[r][c-1]==0&&heights[r][c-1]>=heights[r][c]){
                    visPa[r][c-1] = 1;
                    q.push(make_pair(r,c-1));
                }
                if(c<n-1&&visPa[r][c+1]==0&&heights[r][c+1]>=heights[r][c]){
                    visPa[r][c+1] = 1;
                    q.push(make_pair(r,c+1));
                }
            }
            else{
                visAt[r][c] = 1;
                if(r>0&&visAt[r-1][c]==0&&heights[r-1][c]>=heights[r][c]){
                    visAt[r-1][c] = 1;
                    q.push(make_pair(r-1,c));
                }
                if(r<m-1&&visAt[r+1][c]==0&&heights[r+1][c]>=heights[r][c]){
                    visAt[r+1][c] = 1;
                    q.push(make_pair(r+1,c));
                }
                if(c>0&&visAt[r][c-1]==0&&heights[r][c-1]>=heights[r][c]){
                    visAt[r][c-1] = 1;
                    q.push(make_pair(r,c-1));
                }
                if(c<n-1&&visAt[r][c+1]==0&&heights[r][c+1]>=heights[r][c]){
                    visAt[r][c+1] = 1;
                    q.push(make_pair(r,c+1));
                }
            }
        }
    }

    vector<vector<int>> pacificAtlantic(vector<vector<int>>& heights) {
        int m = heights.size();
        int n = heights[0].size();
        for(int i=0;i<m;i++){
            if(!visPa[i][0]) bfs(i,0,heights,0);
            if(!visAt[i][n-1]) bfs(i,n-1,heights,1);
        }
        for(int i=0;i<n;i++){
            if(!visPa[0][i]) bfs(0,i,heights,0);
            if(!visAt[m-1][i]) bfs(m-1,i,heights,1);
        }
        vector<vector<int>> ans;
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(visAt[i][j]&&visPa[i][j]){
                    vector<int> tmp = {i,j};
                    ans.push_back(tmp);
                }
            }
        }
        return ans;
    }
};
```

