注意事项：linux 系统下对拍指令是 diff，windows 系统下对拍指令是 fc。但是使用方法相同。

假设要对比 A.txt 和 B.txt 是否相等

命令行输入：fc/diff A.txt B.txt

然后如果不相同会输出不相同的地方（有一条指令可以输出不同的行的编号，但是我忘了，以后来补）。

如果不喜欢命令行，可以在 ```#include<bits/stdc++.h>``` 的基础上使用 ```system``` 函数。

具体使用方法（假设为 windows 系统）：```system("fc A.txt B.txt")```。

一般需要造数据对拍。需要 $4$ 个程序：要交的代码，暴力代码，造数据代码和对拍代码。

以 a+b problem 为例。

要交的代码：
```
#include <bits/stdc++.h>
using namespace std;
int main()
{
	freopen("aplusb.in","r",stdin);
	freopen("aplusb.out","w",stdout);
	int a,b;
	cin>>a>>b;
	cout<<a+b;
}
```

暴力代码：
```
#include <bits/stdc++.h>
using namespace std;
int main()
{
	freopen("aplusb.in","r",stdin);
	freopen("aplusbBaoLi.out","w",stdout);
	int a,b;
	cin>>a>>b;
	cout<<a+b;
}
```

造数据代码（名字叫 ```aplusbgen```）：
```
#include <bits/stdc++.h>
using namespace std;
mt19937 Rnd(time(NULL));
int Rand()
{
	return (int)abs((int)Rnd());
}
const int Mod=1e9;//数据范围为1~1e9 
int main()
{
	freopen("aplusb.in","w",stdout);
	cout<<Rand()%Mod+1<<" "<<Rand()%Mod+1;
}
```

对拍代码：
```
#include <bits/stdc++.h>

using namespace std;

int main()
{
	int cas=0;
	while(true)
	{
		cout<<cas<<endl;
		system("aplusbgen.exe");
		system("aplusb.exe");
		system("aplusbBaoLi.exe");
		if(if(system("fc aplusb.out aplusbBaoLi.out"))
		{
			return 0;
		}
	}
}
```
