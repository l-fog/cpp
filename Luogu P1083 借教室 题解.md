### 题解 P1083 借教室

[题目传送门](https://www.luogu.com.cn/problem/P1083)

#### 基本思路

我们先来手动模拟一下样例

| 订单编号 | 第一天剩余可用教室                           | 第二天剩余可用教室                           | 第三天剩余可用教室                            | 第三天剩余可用教室                           |
| -------- | -------------------------------------------- | -------------------------------------------- | --------------------------------------------- | -------------------------------------------- |
| $0$      | $2$                                          | $5$                                          | $4$                                           | $3$                                          |
| $1$      | $2-\color{blue}2\color{black}=\color{gold}0$ | $5-\color{blue}2\color{black}=\color{gold}3$ | $4-\color{blue}2\color{black}=\color{gold}2$  | 3                                            |
| $2$      | $0$                                          | $3-\color{blue}3\color{black}=\color{gold}0$ | $2-\color{blue}3\color{black}=\color{gold}-1$ | $3-\color{blue}3\color{black}=\color{gold}0$ |

我们发现在我们处理到第二张订单时出现了负数，而我们不可能有负数间教室，所以此时就可以跳出循环并判断了。

我们观察一下这个处理的过程，有没有很像区间修改？

那么，是不是可以考虑用线段树？当有小于零的数出现时，我们就可以认为当前订单需要修改。

又因为我们需要查询的时整个区间中是否存在小于零的数，所以线段树维护最小值，且全区间最小就是树根的值~~（连查询都不用写了）~~


#### 实现：线段树+动态开点

用上动态开点总是好的

```cpp
#include <bits/stdc++.h>
using namespace std;
#define GO(__start,__end,__var) for(int __var = __start ; __var<=__end ; __var++)
#define GON(__start,__end,__var,_comp,_ope,_type) for(_type __var = __start ; __var _comp __end ; __var _ope)
#define GOGRA(__edge,__head,__start,__var) for(int __var = __head[__start];__var;__var=__edge[__var].next)
#define COMP(__type,__member,__comp) [=](__type a1,__type a2)->bool{ return a1.__member __comp a2.__member;}
const int maxn=1e6+4,maxm=3000;
typedef long long lld;
typedef double lf;
namespace io{
    inline int fr(){
        register int x=0,dis=1;
        register char ch=getchar();
        while(ch<'0'||ch>'9'){
            if(ch =='-')dis=-1;
            ch=getchar();
        }
        while(ch>='0'&&ch<='9'){
            x=(x<<1)+(x<<3)+(ch^48);
            ch = getchar();
        }
        return x*dis;
    }
    inline lld fr_lld(){
        lld x=0,dis=1;
        char ch=getchar();
        while(ch<'0'||ch>'9'){
            if(ch=='-')dis=-1;
            ch=getchar();
        }
        while(ch>='0'&&ch<='9'){
            x=(x<<1)+(x<<3)+(ch^48);
            ch = getchar();
        }
        return x*dis;
    }
    inline lf fr_lf(){
        lf x=0;int dis =1;lf l=0.1;
        char ch=getchar();
        while(ch<'0'||ch>'9'){
            if(ch=='-')dis=-1;
            ch=getchar();
        }
        while(ch>='0'&&ch<='9'){
            x= 10*x+(ch^48);
            ch=getchar();
        }
        if(ch=='.'){
            ch=getchar();
            while(ch>='0'&&ch<='9'){
                x = x+l*(ch^48);
                l*=0.1;
            }
        }
        return x*dis;
    }
    inline void fw(int num){
        if(num<0){
            putchar('-');
            num=-num;
        }
        if(num>9)fw(num/10);
        putchar(num%10+'0');
    }
    inline void fw_lld(lld num){
        if(num<0){
            putchar('-');
            num=-num;
        }
        if(num>9)fw_lld(num/10);
        putchar(num%10+'0');
    }
}
using namespace io;
int t[maxn<<1],lazy[maxn<<1],lson[maxn<<1],rson[maxn<<1];//动态开点线段树的变量
int cnt=1;
inline void Pushup(int k){t[k]=min(t[lson[k]],t[rson[k]]);}//求最小值
inline void Pushdown(int k,int l,int r){
    if(lazy[k]){
        if(!lson[k])lson[k]=++cnt;
        if(!rson[k])rson[k]=++cnt;
        int mid=(l+r)>>1;
        t[lson[k]]-=lazy[k];//既然每一个值都减去lazy[k], 那么数值之间的相对大小并不会发生改变, 所以最小值也会减去lazy[k]
        t[rson[k]]-=lazy[k];
        lazy[lson[k]]+=lazy[k];
        lazy[rson[k]]+=lazy[k];
        lazy[k]=0;
    }
}
inline void build(int l,int r,int &k,int v,int x){//用单点修改的方法建树
    if(!k)k=++cnt;
    if(l==r)t[k]=x;
    else{
        int mid=(l+r)>>1;
        if(v<=mid)build(l,mid,lson[k],v,x);
        else build(mid+1,r,rson[k],v,x);
        Pushup(k);
    }
}
inline void update(int L,int R,int l,int r,int &k,int x){
    if(!k)k=++cnt;
    if(L<=l&&R>=r){
        t[k]-=x;//见Pushdown()中的注释
        lazy[k]+=x;
        return;
    }
    Pushdown(k,l,r);
    int mid = (l+r)>>1;
    if(L<=mid)update(L,R,l,mid,lson[k],x);
    if(R>mid)update(L,R,mid+1,r,rson[k],x);
    Pushup(k);
}
int n,m;
inline void Read(){
    n=fr();m=fr();//快读
    int temp=1;
    GO(1,n,i)build(1,n,temp,i,fr());//偷懒压行
    return;
}
inline void Solve(){
    GO(1,m,i){
        int d=fr(),s=fr(),j=fr(),temp=1;
        update(s,j,1,n,temp,d);
        if(t[1]<0){//t[1]就是全局最小值
            putchar('-');putchar('1');putchar('\n');fw(i);return;//卡常
        }
    }
    putchar('0');
    return;
}
int main(){
    // freopen("Chorse.in","r",stdin);
    // freopen("Chores.out","w",stdout);
    Read();
    Solve();
    return 0;
}
```

~~其实大部分都是模板~~

代码自带注释，自己看吧
