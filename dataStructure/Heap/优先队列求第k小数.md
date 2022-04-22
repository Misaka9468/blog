# 题目

有一个长度为n的数组，值为 a[i], 牛牛想找到数组中第 k 小的数。比如 1 2 2 3 4  6 中，第 3 小的数就是2.

牛牛觉得这个游戏太简单了，想加一点难度，现在牛牛有 m 个操作，每个操作有两种类型。

1 x  1 代表操作一，给数组中加一个元素 x 。(0 ≤ x ≤ 1e9)

2  k   2 代表操作二，查询第 k 小的数。如果没有 k 个数就输出−1



# 思路

* 动态
* 最值

完美的优先队列问题

如何维护第k小呢？反而要用大根堆，因为每次要和堆顶比较。大的等于的丢掉，小的插入。

那么堆顶就是第k小。

# 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
ll n,m,k,d,s,x,cnt=0,mas[200010];
priority_queue<int,vector<int>,less<int>> q;
 
int main()
{ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);
	cin>>n>>m>>k;
	for(int i=1;i<=n;++i){
		cin>>mas[i];
	}
	sort(mas+1,mas+1+n);
	for(int i=1;i<=n;++i){
		q.push(mas[i]);
		if(q.size()>k) q.pop();
	}
	for(int i=1;i<=m;++i){
		cin>>d;
		if(d==1){
			cin>>s;
			q.push(s);
		    if(q.size()>k) q.pop();
		} 
		else if(d==2){
			if(q.size()<k) cout<<-1<<endl;
			else{
				cout<<q.top()<<endl;
			}
		}
	}
	return 0;
}
```

