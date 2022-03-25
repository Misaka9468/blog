# trick1 dfs参数在回溯中

对于需要回溯的、不为数组的全局变量，完全可以用dfs的参数来代替。这样避免了手动回溯。

举例：https://www.luogu.com.cn/problem/P1378

题意：在二维平面上滴不同位置的油滴，其会扩散成一个圆，直至遇到边界或其他油滴。求滴油滴的顺序，使得油滴占据总面积最大。



这里直接将计算圆的面积作为了参数，避免了回溯时的减去

并且在main里只调用了一次dfs，这是个好方法模板


代码：

```cpp
#include<iostream>
#include<cstring>
#include<string>
#include<vector>
#include<algorithm>
#include<cmath>

using namespace std;
#define PI 3.1415926535898
int N;
double a,b,c,d;
double x_1,y_1,x_2,y_2;
double x[8],y[8];
bool vis[8];
double len[8];
double max_square=0;
double now_square=0;

double dist(double x_1,double y_1,double x_2,double y_2){
    return 1.0*sqrt((x_2-x_1)*(x_2-x_1)+(y_2-y_1)*(y_2-y_1));
}

double solve(int idx){
    double xi = x[idx],yi=y[idx];
    double min_r = min(min(x_2-xi,xi-x_1),min(y_2-yi,yi-y_1));
    for(int i=0;i<N;i++){
        if(i!=idx&&vis[i]){
            double d=dist(xi,yi,x[i],y[i]);
            min_r=min(min_r,max(d-len[i],0.0));
        }
    }
    return min_r;
}
void dfs(int idx,double sum){
    if(idx>N){
        max_square=max(max_square,sum);
        return ;
    }
    for(int i=0;i<N;i++){
        if(!vis[i]){
            len[i] = solve(i);
            vis[i] =1; // 搜索
            dfs(idx+1,sum+PI*len[i]*len[i]);
            vis[i] = 0; // 回溯
        }
    }
}

int main(){
    scanf("%d",&N);
    scanf("%lf%lf%lf%lf",&a,&b,&c,&d);
    // 强行划分为左下与右上
    x_1 = min(a,c),x_2 = max(a,c);
    y_1 = min(b,d),y_2 = max(b,d);
    for(int i=0;i<N;i++){
        scanf("%lf%lf",&x[i],&y[i]);
    }
    dfs(1,0);
    printf("%d",(int)((y_2-y_1)*(x_2-x_1)-max_square+0.5));
}

```