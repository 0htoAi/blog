```
#include <bits/stdc++.h>
#define ll long long
using namespace std;
const int MAXN=2e5+50,M=20;
int N;
int father[MAXN];
int Ancester[MAXN];
struct Edge
{
	int x,y,Next;
}e[MAXN<<1];
int elast[MAXN],tot;
void Add(int x,int y)
{
	tot++;
	e[tot].x=x;
	e[tot].y=y;
	e[tot].Next=elast[x];
	elast[x]=tot;
}
ll ans[MAXN];
ll f[MAXN],g[MAXN];
vector<int>Vec[MAXN];
void dfs(int u,int Id)
{
	Vec[Ancester[u]].push_back(u);
	g[u]=0;
	f[u]=0;
	for(int i=elast[u];i;i=e[i].Next)
	{
		int v=e[i].y;
		dfs(v,Id);
		ans[Id]+=f[u]*g[v];
		f[u]+=g[v];
	}
	for(int i=0;i<Vec[u].size();i++)
	{
		g[u]+=f[Vec[u][i]]+1;
	}
}
void dfs2(int u)
{
	
	for(int i=elast[u];i;i=e[i].Next)
	{
		int v=e[i].y;
		dfs2(v);
		if(Vec[v].size()>Vec[u].size())
		{
			Vec[v].swap(Vec[u]);
		}
		for(int j=M+1;j<=Vec[v].size();j++)
		{
			ll Sum1=0,Sum2=0;
			for(int k=j;k<=Vec[v].size();k+=j)
			{
				Sum1+=Vec[v][Vec[v].size()-k];
			}
			for(int k=j;k<=Vec[u].size();k+=j)
			{
				Sum2+=Vec[u][Vec[u].size()-k];
			}
			ans[j]+=Sum1*Sum2;
		}
		for(int j=1;j<=Vec[v].size();j++)
		{
			Vec[u][Vec[u].size()-j]+=Vec[v][Vec[v].size()-j];
		}
	}
	Vec[u].push_back(1);
}

int depth[MAXN];
int Max=0;
ll ans1[MAXN];
int main()
{
	scanf("%d",&N);
	for(int i=2;i<=N;i++)
	{
		scanf("%d",&father[i]);
		Add(father[i],i);
		Ancester[i]=i;
		depth[i]=depth[father[i]]+1;
		ans1[depth[i]]++;
		Max=max(Max,depth[i]);
	}
	for(int i=N;i>=1;i--)
	{
		ans1[i]+=ans1[i+1];
	}
	for(int i=1;i<=M;i++)
	{
		dfs(1,i);
		for(int i=0;i<=N;i++)
		{
			Ancester[i]=father[Ancester[i]];
			Vec[i].clear();
		}
	}
	dfs2(1); 
	for(int i=N;i>=1;i--)
	{
		for(int j=2*i;j<=N;j+=i)
		{
			ans[i]-=ans[j];
		}	
	}
	for(int i=1;i<N;i++)
	{
		printf("%lld\n",ans[i]+ans1[i]);
	}	
}
//https://www.cnblogs.com/alfalfa-w/p/15556669.html
```
