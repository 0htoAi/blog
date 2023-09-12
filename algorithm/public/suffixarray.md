# 后缀数组

前情回顾：

[KMP&AC自动机](https://www.cnblogs.com/0htoAi/p/17559065.html)

[马拉车&回文自动机](https://www.cnblogs.com/0htoAi/p/17340598.html)

在写后缀自动机之前先写一个小清新的后缀相关算法，也就是后缀排序。后缀数组记录的就是排序之后的这个序，具体来说有三个重要的数组。

现在有一个字符串 $s$，为了方便记 $s$ 的长度为 $N$。

$s_i$ 表示 $s$ 这个字符串的第 $i$ 个字符。

称一个后缀 $suf_i$ 表示 $s_{i\sim N}$。

现在要把所有的 $N$ 个后缀按照字典序排序，没有字符的位置可以视作比有字符的位置小。

```
例子：
s=abaab
suf[1]=abaab
suf[2]=baab
suf[3]=aab
suf[4]=ab
suf[5]=b
排序后为
aab
ab
abaab
b
baab
```



$SA_i$ 表示第 $suf_i$ 的排名，$Rank_i$ 表示排名为 $i$ 的后缀为 $suf_{Rank_i}$。这两个数组互为反数组。

$Height_i$ 表示 $suf_{Rank_i}$ 和 $suf_{Rank_{i+1}}$ 的最长公共前缀。（$+1$ 和 $-1$ 本质相同，我统一跟 $i+1$ 比）。可以视作把后缀排序之后前后两个后缀的最长公共前缀。

```
上文的例子：
s=abaab
排序后为
aab
ab
abaab
b
baab

SA[1]=3 SA[2]=5 SA[3]=1 SA[4]=2 SA[5]=4
Rank[1]=3 Rank[2]=4 Rank[3]=1 Rank[4]=5 Rank[5]=2
Height[1]=1 Height[2]=2 Height[3]=0 Height[4]=1 
```

值得注意的是，$Height$ 数组代表的是两个数的关系，且只有 $N-1$ 位。不能单纯看做每位对应一个字符串看待，在某些题目需要特殊处理。

后缀数组与后缀树的关系会在后文提到。下述的性质也可以通过后缀树进行理解。

单纯从后缀数组考虑，也可以得出很多基础性质。

* 设 $Rank_i < Rank_j,(i\neq j)$，$suf_i$ 和 $suf_j$ 的最长公共前缀为 $\min_{k=Rank_i}^{Rank_j-1}(Height_k)$。

  因为最长公共前缀是具有传递性的，通过上面的例子也可以看出来。所以可以用区间 RMQ 相关的一切算法。

* 本质不同子串个数及子串排序

  考虑到任意子串可以看做一个后缀的前缀，且一一对应。而一个后缀的前缀的字典序一定是越短字典序越小。在已经求出后缀数组的基础上，一个 $Rank$ 为  $i$ 的后缀能贡献的**后续不会出现**的子串数量为 $N-SA_i+1-Height_i$。这里可以视作 $Height_N=0$。相当于一个子串只在后缀排序之后最后一个有这个子串出现的后缀中统计，这样不会算重。

  要求字典序第 $k$ 大的子串，跟上述的略有区别，每个子串需要在后缀排序之后第一个有这个子串的后缀中就被统计，$Rank$ 为 $i$ 个后缀贡献为 $N-SA_i+1-Height_{i-1}$。然后可以用求字典序常规操作，通过预处理每个后缀贡献的子串前缀和，即可简单求得。

* 相同子串

  长度为 $x$ 的相同子串可以看做一串连续的 $Height\geq x$。最多出现次数即为极长的区间满足 $\min_{i=l}^{r}(Height_{i})\geq x$。单调栈。值得注意的是最多出现次数为 $r-l+2$ 因为最后一个 $Height_i$ 会管辖到 $i+1$ 位。

  相反的，如果求最长的子串满足重复出现了 $x$ 次，则可以视作在所有长度为 $x-1$ 的 $Height$ 数组的区间中找到 $\min(Height)$ 最大的一个区间。

  有些题目会要求重复的子串不能相交，如果重复次数为 $2$ 有个较为简单的做法，记录 $\min(SA_{Rank_i})$ 和 $\max(SA_{Rank_i})$，对于一个长度为 $x$ 的子串，如果不相交，则在上述说到的那个极长区间中还需要满足 $\min_{i=l}^{r}(SA_{Rank_i})+x-1 < \max_{i=l}^{r}(SA_{Rank_i})$。

* 按照 $Height$ 从大到小加入然后维护连通区间可以解决很多字符串贪心问题，维护联通区间可以用并查集。这样合并的时候还可以维护这个区间的最长重复子串（即为当前加入的 $Height$）。不过也需要注意 $Height$ 其实代表的是两个字符串这个细节。

* 多串之间的子串和后缀关系可以通过把多串连在一起考虑来处理。具体地，把串头尾相接，中间插入**不在字符集中且互不相同**的字符即可。然后就视作一个字符串的不同区域了。

为了方便写，两个字符串 $s1$ 和 $s2$ 的最长公共前缀为 $\text{lcp}(s1,s2)$，最长公共后缀为 $\text{lcs}(s1,s2)$。$Height_i$ 可以表示为 $\text{lcp}(suf_i,suf_{i+1})$。



## 后缀排序的简易实现

最简单的实现方式是倍增。考虑[基数排序](https://www.cnblogs.com/0htoAi/p/16756138.html)的过程，每次把 $N$ 个后缀按照两个关键字排序，设当前排序的长度为 $Len$，也就是把所有的 $s_{i\sim i+Len-1}$ 拿来排序，而由于上一次排序的长度为 $\frac{Len}{2}$（初始化可以直接处理出 $Len$ 为 $1$ 的排序情况），所以每个 $i$ 有两个关键字，第一关键字为为上一轮 $s_{i\sim i+\frac{Len}{2}-1}$ 的排名，第二关键字为 $s_{i+\frac{Len}{2}\sim i+Len-1}$。先按照第一关键字排序再按照第二关键字排序即可求出当前的排名。

我的大常数实现方式：

```
for(int i=1;i<=N;i++)
{
	Rank[i]=a[i];
	MaxRank=max(MaxRank,a[i]);
}
for(int Len=1;Len<=N;Len<<=1)
{
	for(int i=0;i<=MaxRank;i++)
	{
		Sum[i]=Cnt[i]=0;
	}
	for(int i=1;i<=N;i++)
	{
		Sum[Rank[i+Len]]++;
	}
	for(int i=1;i<=MaxRank;i++)
	{
		Sum[i]+=Sum[i-1];
	}
	for(int i=1;i<=N;i++)
	{
		int Begin=(Rank[i+Len]?Sum[Rank[i+Len]-1]:0);
		Cnt[Rank[i+Len]]++;
		Id[Begin+Cnt[Rank[i+Len]]]=i;
	}
	for(int i=0;i<=MaxRank;i++)
	{
		Sum[i]=Cnt[i]=0;
	}
	for(int i=1;i<=N;i++)
	{
		Sum[Rank[Id[i]]]++;
	}
	for(int i=1;i<=MaxRank;i++)
	{
		Sum[i]+=Sum[i-1];
	}
	for(int i=1;i<=N;i++)
	{
		Cnt[Rank[Id[i]]]++;
		Next[Sum[Rank[Id[i]]-1]+Cnt[Rank[Id[i]]]]=Id[i];
	}
	MaxRank=0;
	for(int i=1;i<=N;i++)
	{
		if(i==1||Rank[Next[i]]!=Rank[Next[i-1]]||Rank[Next[i]+Len]!=Rank[Next[i-1]+Len])
		{
			MaxRank++;
		}
		Id[Next[i]]=MaxRank;
	}
	for(int i=1;i<=N;i++)
	{
		Rank[i]=Id[i];
	}
	if(MaxRank==N)
	break;
}
for(int i=1;i<=N;i++)
{
	SA[Rank[i]]=i;
}
```

有更好的实现方法。

考虑在一个很好的时间复杂度内求 $Height$ 数组。考虑 $s$ 中相邻两个后缀之间的关系，后面一个后缀为前面一个后缀的一段从第 $2$ 位开始的前缀。设当前已经求出 $Height_{Rank_{i-1}}$，考虑求 $Height_{Rank_i}$。在 $s$ 上 $s_{i-1}$ 至 $s_{i-1+Height_{Rank_{i-1}}-1}$ 都肯定是在其它地方出现过的，那么 $s_{i}$ 至 $s_{i-1-Height_{Rank_{i-1}}-1}$ 肯定都有，所以 $Height_{Rank_i}$ 至少为 $Height_{Rank_{i-1}}-1$。注意 $Height_{Rank_{i-1}}=0$ 时要判一下。然后暴力增加 $Height_{Rank_{i}}$ 的长度，判断合不合法。注意到这样做相当于双指针在 $s$ 上扫了一遍，时间复杂度是 $O(N)$ 的。 

我的实现方式：

```
for(int i=1;i<=N;i++)
{
	Height[Rank[i]]=max(0,Height[Rank[i-1]]-1);
	while(i+Height[Rank[i]]<=N&&a[i+Height[Rank[i]]]==a[SA[Rank[i]+1]+Height[Rank[i]]])
	{
		Height[Rank[i]]++;
	}
}
```



后缀数组和后缀树的关系我估计会放到 SAM 博客里面讲。其实可以发现上文说到一个```按照 Height 从大到小加入然后维护连通区间```其实就是在建后缀树！

写完 SAM 博客后再写例题。

