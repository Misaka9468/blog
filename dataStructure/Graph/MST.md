# Prim



Prim相较于Dijkstra, 有以下不同：

* 要双向建边
* 松弛的式子不一样：
  * if( dis[v] > d(u,v) ) then dis[v] = d(u,v)   
  * 这里的dis不是到源点的距离，应该理解为是到 **已选取所有节点所组成的集合** 的最小距离



朴素版的时间复杂度$O(V^2)$ ， 优先队列优化版的$O(|E|logV)$



整体而言，可以通过dijkstra魔改。还是kruskal好用. 



```java
class Solution {
    public int minimumCost(int n, int[][] connections){
        List<List<int[]>> graph = new ArrayList<>();
        for(int i = 0; i < n; i++) graph.add(new ArrayList<>());
        for(int[] edge : connections){
            int u = edge[0] - 1, v = edge[1] - 1, weight = edge[2];
            graph.get(u).add(new int[]{v, weight});
            graph.get(v).add(new int[]{u, weight});
        }
        int sum = 0, INF = Integer.MAX_VALUE;
        int[] dists = new int[n];
        boolean[] visited = new boolean[n];
        Arrays.fill(dists, INF);
        dists[0] = 0; // 生成树起始点距离置0 (可任选一个顶点)
        int u = -1; // 当前距离未确定顶点中的距离最小者
        
        // 这里直接用|V|-1次外部循环也可以
        while((u = getMin(dists, visited)) != -1) { // 遍历顶点，寻找当前距离未确定顶点中的距离最小者
            visited[u] = true; // 立即置为距离已确定
            for (int[] v_weight : graph.get(u)) {
                int v = v_weight[0], weight = v_weight[1];
                if(!visited[v]) { // 对于u的距离未确定的邻接顶点v，更新v的距离
                    if(weight < dists[v]) { // 更新条件
                        dists[v] = weight; // 更新dv
                    }
                }
            }
        }
        for(int dist : dists){ // 计算 MST 边权和
            if(dist == INF) return -1; // 若有不连通的城市，返回 -1
            sum += dist;
        }
        return sum;
    }
    private int getMin(int[] dists, boolean[] visited){ // 在当前距离未确定的顶点中找距离最小者
        int n = dists.length, min = Integer.MAX_VALUE, minVertex = -1;
        for (int u = 0; u < n; u++) {
            if(!visited[u] && dists[u] < min) { 
                min = dists[u];
                minVertex = u;
            }
        }
        return minVertex;
    }
}

作者：yukiyama
链接：https://leetcode.cn/circle/discuss/FyPTTM/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



```java
class Solution {
    public int minimumCost(int n, int[][] connections){
        List<List<int[]>> graph = new ArrayList<>();
        for(int i = 0; i < n; i++) graph.add(new ArrayList<>());
        for(int[] edge : connections){ // 建带权图
            int u = edge[0] - 1, v = edge[1] - 1, weight = edge[2];
            graph.get(u).add(new int[]{v, weight}); // 建边 (u,v)
            graph.get(v).add(new int[]{u, weight}); // 建边 (v,u)
        }
        int sum = 0, INF = Integer.MAX_VALUE; // sum为MST边权和，即本题所求。INF为所有顶点距离初始值。
        int[] dists = new int[n];
        boolean[] visited = new boolean[n];
        Arrays.fill(dists, INF); // 距离初始化
        dists[0] = 0; // 生成树起始点距离置0 (可任选一个顶点)
        PriorityQueue<int[]> pq = new PriorityQueue<>((u, v) -> u[1] - v[1]); // 小顶堆
        pq.add(new int[]{0, 0}); // 起始点入堆
        while(!pq.isEmpty()) { 
            int[] u_dist = pq.remove();
            int u = u_dist[0];
            if(visited[u]) continue;
            visited[u] = true; // 立即置u的距离为已确定
            for (int[] v_weight : graph.get(u)) {
                int v = v_weight[0], weight = v_weight[1];
                if(!visited[v]) { // 对于u的距离未确定的邻接顶点v，更新v的距离
                    if(weight < dists[v]) { // 更新条件
                        dists[v] = weight; // 更新dv
                        pq.add(new int[]{v, weight}); // v入堆
                    }
                }
            }
        }
        for(int dist : dists){ // 计算 MST 边权和
            if(dist == INF) return -1; // 若有不连通的城市，返回 -1
            sum += dist;
        }
        return sum;
    }
}

作者：yukiyama
链接：https://leetcode.cn/circle/discuss/FyPTTM/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



# Kruskal

$O(ElogE+V)$  前者是因为边排序, 可以化简为$O(ElogV)$



最小生成树特有的建图方式，以边为本.



```c++
#include<iostream>
#include<algorithm>

using namespace std;
#define MAXN 200010

struct edge{
    int s,v,L;
    bool operator < (const struct edge& b) const{
        return L<b.L;
    }
}edges[MAXN];

int fa[MAXN];

int N,M,K,X,Y,L;
int tot_cost;
int cnt;

int find_fa(int a){
    if(fa[a]==a) return a;
    else 
        return (fa[a] = find_fa(fa[a]));
}

int main(){
    scanf("%d %d",&N,&M);
    for(int i=0;i<N;i++){
        fa[i] = i;
    }
    for(int i=0;i<M;i++){
        scanf("%d %d %d",&edges[i].s,&edges[i].v,&edges[i].L);
    }
    sort(edges,edges+M);
    for(int i=0;i<M;i++){
        if(find_fa(edges[i].s)!=find_fa(edges[i].v)){
            fa[find_fa(edges[i].s)] = find_fa(edges[i].v);
            tot_cost += edges[i].L;
            cnt++;
        }
    }
    if(cnt==N-1)
        printf("%d",tot_cost);
    else printf("orz");
    return 0;
}
```

