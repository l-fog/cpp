# P1434 滑雪 题解

~~感觉这题好水~~ **我是蒟蒻**

题目传送门：[P1434](https://www.luogu.com.cn/problem/P1434)

这道题就是妥妥的动规

> **图论背模板，数论背公式，动规背方程，高精背代码**

#### 一、定义状态

$dp[i][j]$走到点$(i,j)$的长距离（包括点$(i,j)$）

#### 二、分析问题

对于一个点$(i,j)$，显然有以下四种可能的走法：

`向上` `向下` `向左` `向右` 

那么只要目标点可走，就可以计算目标点的$dp$

#### 三、转移方程

$设dp[i][j]代表点(i,j)的dp,a[i][j]代表点(i,j)的高度$

`dp[i+1][j]=max(dp[i][j]+1,dp[i+1][j])(a[i][j]>a[i+1][j])`

`dp[i-1][j]=max(dp[i][j]+1,dp[i-1][j])(a[i][j]>a[i-1][j])`

`dp[i][j+1]=max(dp[i][j]+1,dp[i][j+1])(a[i][j]>a[i][j+1])`

`dp[i][j-1]=max(dp[i][j]+1,dp[i][j-1])(a[i][j]>a[i][j-1])`

~~为什么有四条~~

因为这是在一张图中找最长路，所以应该用`DFS(Depth First Search)`扫描整张图

同时由于题目没有给出起点，所以要枚举每一个点作为起点

所以只需要设置起点的$dp$为1，然后`DFS`就好了

#### 四、优化

我们发现，如果枚举每一个点作为起点，每次都搜索整张图的话肯定$\text{TLE}$掉。

所以要考虑一下如何优化。

我们发现，好像从最高的地方开始下滑得到的长度最大？

试一下 ~~试试就逝世~~ 

```c++
#include<bits/stdc++.h>
#define GO(from,to,var) for(int var=from;var<=to;var++)
#define GON(from,to,var) for(int var=from;var<to;var++)
#define GOGRA(EDGE,HEAD,var,u) for(int var = HEAD[u];var;var=EDGE[var].next)
#define COMP(__type,__member,__comp) [=](__type a1,__type a2)->bool{return a1.__member __comp a2.__member;}
using namespace std;
typedef int NUM;
const NUM maxn = 1e7;const NUM maxm = 1e7;
void Read();void Solve();
inline NUM fr(){
    NUM x = 0, dis = 1;
    char ch = getchar();
    while(ch<'0'||ch>'9'){
        if(ch == '-')dis = -1;
        ch = getchar();}
    while(ch>='0'&&ch<='9'){
        x=(x<<1)+(x<<3)+(ch^48);
        ch = getchar();}
    return x*dis;
}
int a[3000][3000];
int dp[3000][3000];
int stn,stm,n,m,maxx;

inline void dfs(int nn,int mm){
    if(nn>0&&mm>0&&nn<=n&&mm<=m){//合法的点
        maxx=max(maxx,dp[nn][mm]);
        if(a[nn+1][mm]<a[nn][mm]&&dp[nn+1][mm]<dp[nn][mm]+1){
            dp[nn+1][mm]=dp[nn][mm]+1;
            dfs(nn+1,mm);
        }
        if(a[nn-1][mm]<a[nn][mm]&&dp[nn-1][mm]<dp[nn][mm]+1){
            dp[nn-1][mm]=dp[nn][mm]+1;
            dfs(nn-1,mm);
        }
        if(a[nn][mm+1]<a[nn][mm]&&dp[nn][mm+1]<dp[nn][mm]+1){
            dp[nn][mm+1]=dp[nn][mm]+1;
            dfs(nn,mm+1);
        }
        if(a[nn][mm-1]<a[nn][mm]&&dp[nn][mm-1]<dp[nn][mm]+1){
            dp[nn][mm-1]=dp[nn][mm]+1;
            dfs(nn,mm-1);
        }
    }
}



void Read(){
    memset(a,0x3f,sizeof(a));
    n=fr(),m=fr();int maxxx=0;
    GO(1,n,i)GO(1,m,j){
        a[i][j]=fr();
        if(maxxx<a[i][j]){
            maxxx=a[i][j];
            stn=i;stm=j;
        }
    }
}
void Solve(){
    dp[stn][stm]=1;
    dfs(stn,stm);
    printf("%d",maxx);
}
int main(){
    Read();
    Solve();
    return 0;
}
```

![50pts](https://cdn.luogu.com.cn/upload/image_hosting/72lzg75z.png)

这是为什么呢？

![img](https://cdn.luogu.com.cn/upload/pic/37472.png)

不要急，我们先来构造一组数据：

```
5 5
1 2 3 4 5
1 1 1 1 1
1 1 1 9 1
1 1 1 1 1
1 1 1 1 1
```

我们发现，虽然$(3,4)$是最高的点，但是最长路径的起点显然应该是$(1,5)$

难道我们`DFS`真的只能眼睁睁的看着它$\text{TLE}$了吗?

不要慌，我们仔细思考一下：

在跑第一遍`DFS`的时候，我们就已经处理了一部分点的最长长度。换言之，一部分点的$dp$已经求出来了（未必正确）。

所以，我们只需要把未处理过的点当成起点跑`DFS`就可以了。

注意起点的$dp=1$哟。

#### 五、AC代码

~~我知道你们就想要这个~~

```c++
#include<bits/stdc++.h>
#define GO(from,to,var) for(int var=from;var<=to;var++)
#define GON(from,to,var) for(int var=from;var<to;var++)
#define GOGRA(EDGE,HEAD,var,u) for(int var = HEAD[u];var;var=EDGE[var].next)
#define COMP(__type,__member,__comp) [=](__type a1,__type a2)->bool{return a1.__member __comp a2.__member;}
using namespace std;
typedef int NUM;
const NUM maxn = 1e7;const NUM maxm = 1e7;
void Read();void Solve();
inline NUM fr(){
    NUM x = 0, dis = 1;
    char ch = getchar();
    while(ch<'0'||ch>'9'){
        if(ch == '-')dis = -1;
        ch = getchar();}
    while(ch>='0'&&ch<='9'){
        x=(x<<1)+(x<<3)+(ch^48);
        ch = getchar();}
    return x*dis;
}
//个人模板

int a[3000][3000];//原图
int dp[3000][3000];//dp[i][j]-> 走到(i,j)的最长路
int n,m,maxx;//maxx记录答案

inline void dfs(int nn,int mm){
    if(nn>0&&mm>0&&nn<=n&&mm<=m){//合法的点
        maxx=max(maxx,dp[nn][mm]);//记录
        if(a[nn+1][mm]<a[nn][mm]&&dp[nn+1][mm]<dp[nn][mm]+1){//可以向下
            dp[nn+1][mm]=dp[nn][mm]+1;
            dfs(nn+1,mm);
        }
        if(a[nn-1][mm]<a[nn][mm]&&dp[nn-1][mm]<dp[nn][mm]+1){//可以向上
            dp[nn-1][mm]=dp[nn][mm]+1;
            dfs(nn-1,mm);
        }
        if(a[nn][mm+1]<a[nn][mm]&&dp[nn][mm+1]<dp[nn][mm]+1){//可以向右
            dp[nn][mm+1]=dp[nn][mm]+1;
            dfs(nn,mm+1);
        }
        if(a[nn][mm-1]<a[nn][mm]&&dp[nn][mm-1]<dp[nn][mm]+1){//可以向左
            dp[nn][mm-1]=dp[nn][mm]+1;
            dfs(nn,mm-1);
        }
    }
}

void Read(){
    memset(a,0x3f,sizeof(a));//初始化
    n=fr(),m=fr();
    GO(1,n,i)GO(1,m,j){//读入数据
        a[i][j]=fr();
    }
}
void Solve(){
    GO(1,n,i)GO(1,m,j){
        if(dp[i][j]==0){
            dp[i][j]=1;//起点的dp至少为1
            dfs(i,j);
        }
    }
    printf("%d",maxx);//输出
}
int main(){
    Read();
    Solve();
    return 0;
}
```
