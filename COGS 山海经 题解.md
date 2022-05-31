### COGS 山海经 题解

#### 题目大意

> 求一个数列 $\{a_n\}$ 中任意几个区间的最大子段和

#### 基本思路

不能直接用 $dp$ 暴力解，会 $TLE$ 

考虑构造一个线段树，维护对应区间的最大子段和。

思考一下，要求出一个区间的最大子段和需要些什么？

先来思考会有哪些情况：

$Case1$ ：直接就是左区间或者右区间的最大子段和

```plain text
e.g.
lson: 1 2 3 4 5 -100
rson: -100 0 -100 -100 -100 -100
显然应该以左区间的最大子段和作为这个区间的最大子段和
```

$Case2$ ：跨区间的最大子段和

```Plain text
e.g.
lson: 1 2 -100 2
rson: 4 -100 1 4
左区间的最大子段和是(1,2)=3，右区间的最大子段和是(7,8)=5
但是总区间的最大子段和是(4,5)=6
```

一共两种情况

在第二种情况下，我们需要知道些什么呢？

应当是左区间中包含右端点的最大子段和以及右区间中包含左端点的最大子段和，对吧？

那么我们就明确的需要知道的条件：

**左右区间的最大子段和以及分别包含左右端点的最大子段和**

好的，左右区间的最大子段和好求，但是包含左右端点的最大子段和怎么求？

其实很简单，无非以下几种情况：

```Plain text
我们约定用lmx表示包含左端点的最大子段和，rmx表示包含右端点的最大子段和，val表示区间和
fa(lmx)表示总区间的lmx，lson(rmx)表示左区间的rmx，以此类推
Case1: fa(lmx)=lson(lmx),fa(rmx)=rson(rmx)
Case2: fa(lmx)=lson(lmx),fa(rmx)=rson(val)+lson(rmx)
Case3: fa(lmx)=lson(val)+rson(lmx),fa(rmx)=rson(rmx)
Case4: fa(lmx)=lson(val)+rson(lmx),fa(rmx)=rson(val)+lson(rmx)
```

所以我们维护的线段树中要增加一个条件：区间和

#### 核心代码

```cpp
#include <bits/stdc++.h>
using namespace std;
#define GO(__start,__end,__var) for(int __var = __start ; __var<=__end ; __var++)
const int maxn=1e5 + 12,maxm=1e5 + 12;
typedef long long lld;
typedef double lf;
namespace io{
    //快读模板
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
    //快写模板
    inline void fw(int num){
        if(num<0){
            putchar('-');
            num=-num;
        }
        if(num>9)fw(num/10);
        putchar(num%10+'0');
    }
    inline void fw(int num,char backch){
        fw(num);putchar(backch);
    }
}
using namespace io;
//线段树节点
struct Node{
    int mx;//最大子段和
    int ll,rr;//最大子段和区间
    
    int lmx,rs;//左起最大,右边界
    int rmx,ls;//右起最大，左边界
    int val;//和
}t[maxn<<1];
int a[maxn];//原数组
int lson[maxn<<1],rson[maxn<<1],tot=1;//动态开点
int n,m;
inline void Pushup(int k){
    t[k].val=t[lson[k]].val+t[rson[k]].val;//维护区间和
    
    //维护lmx,rmx
    if(t[lson[k]].lmx<t[lson[k]].val+t[rson[k]].lmx){
        t[k].lmx=t[lson[k]].val+t[rson[k]].lmx;
        t[k].rs=t[rson[k]].rs;
    }else{
        t[k].lmx=t[lson[k]].lmx;
        t[k].rs=t[lson[k]].rs;
    }
    if(t[rson[k]].rmx<t[rson[k]].val+t[lson[k]].rmx){
        t[k].rmx=t[rson[k]].val+t[lson[k]].rmx;
        t[k].ls=t[lson[k]].ls;
    }else{
        t[k].rmx=t[rson[k]].rmx;
        t[k].ls=t[rson[k]].ls;
    }
	
    
    //因为题目要求输出字典序最小，所以要算两遍，比较一下
    //先计算Case1，再计算Case2
    Node temp=t[k];
    if(t[lson[k]].mx>=t[rson[k]].mx){
        temp.mx=t[lson[k]].mx;
        temp.ll=t[lson[k]].ll;
        temp.rr=t[lson[k]].rr;
    }else{
        temp.mx=t[rson[k]].mx;
        temp.ll=t[rson[k]].ll;
        temp.rr=t[rson[k]].rr;
    }
    if(temp.mx<t[lson[k]].rmx+t[rson[k]].lmx){
        temp.mx=t[lson[k]].rmx+t[rson[k]].lmx;
        temp.ll=t[lson[k]].ls;
        temp.rr=t[rson[k]].rs;
    }

    t[k].mx=t[lson[k]].rmx+t[rson[k]].lmx;
    t[k].ll=t[lson[k]].ls;
    t[k].rr=t[rson[k]].rs;

    if(t[lson[k]].mx>=t[k].mx){
        t[k].mx=t[lson[k]].mx;
        t[k].ll=t[lson[k]].ll;
        t[k].rr=t[lson[k]].rr;
    }
    if(t[rson[k]].mx>t[k].mx){
        t[k].mx=t[rson[k]].mx;
        t[k].ll=t[rson[k]].ll;
        t[k].rr=t[rson[k]].rr;
    }
	//比较字典序
    if(t[k].ll>=temp.ll)t[k]=temp;
}
//动态开点建树
inline void build(int &k,int l,int r){
    if(!k)k=++tot;
    if(l==r){
        t[k].ls=t[k].ll=l;
        t[k].rr=t[k].rs=r;
        t[k].val=a[l];
        t[k].lmx=t[k].rmx=t[k].mx=a[l];
        return;
    }
    int mid=(l+r)>>1;
    build(lson[k],l,mid);
    build(rson[k],mid+1,r);
    Pushup(k);
}
inline Node query(int k,int l,int r,int L,int R){
    if(L<=l&&R>=r)return t[k];
    int mid=(l+r)>>1;
    //这里要做一下特判
    //如果要求的区间都在左区间或右区间就珂以直接返回，没必要比较
    if(R<=mid)return query(lson[k],l,mid,L,R);
    if(L>mid)return query(rson[k],mid+1,r,L,R);
    //分别查询左右
    Node lans=query(lson[k],l,mid,L,R);
    Node rans=query(rson[k],mid+1,r,L,R);
    //初始化ans
    Node ans=(Node){0,0,0,0,0,0,0,0};
	
    //维护val,lmx,rmx
    ans.val=lans.val+rans.val;
    if(lans.lmx<lans.val+rans.lmx){
        ans.lmx=lans.val+rans.lmx;
        ans.rs=rans.rs;
    }else{
        ans.lmx=lans.lmx;
        ans.rs=lans.rs;
    }
    if(rans.rmx<rans.val+lans.rmx){
        ans.rmx=rans.val+lans.rmx;
        ans.ls=lans.ls;
    }else{
        ans.rmx=rans.rmx;
        ans.ls=rans.ls;
    }
	
    //同上，需要进行比较
    Node temp=ans;
    if(lans.mx>=rans.mx){
        temp.mx=lans.mx;
        temp.ll=lans.ll;
        temp.rr=lans.rr;
    }else{
        temp.mx=rans.mx;
        temp.ll=rans.ll;
        temp.rr=rans.rr;
    }
    if(temp.mx<lans.rmx+rans.lmx){
        temp.mx=lans.rmx+rans.lmx;
        temp.ll=lans.ls;
        temp.rr=rans.rs;
    }
    
    ans.mx=lans.rmx+rans.lmx;
    ans.ll=lans.ls;
    ans.rr=rans.rs;
    if(lans.mx>=rans.mx&&lans.mx>ans.mx){
        ans.mx=lans.mx;
        ans.ll=lans.ll;
        ans.rr=lans.rr;
    }
    if(rans.mx>ans.mx){
        ans.mx=rans.mx;
        ans.ll=rans.ll;
        ans.rr=rans.rr;
    }
    if(ans.ll>=temp.ll)ans=temp;
    return ans;
}
inline void Read(){
    n=fr(),m=fr();

    GO(1,n,i)a[i]=fr();
    int temp=1;
    build(temp,1,n);
    return;
}
inline void Solve(){
    GO(1,m,i){
        int l=fr(),r=fr();
        Node ans=query(1,1,n,l,r);
        fw(ans.ll,' ');
        fw(ans.rr,' ');
        fw(ans.mx,'\n');
    }
    return;
}
signed main(){
    // freopen("Chorse.in","r",stdin);
    // freopen("Chores.out","w",stdout);
    Read();
    Solve();
    // fclose(stdin);
    // fclose(stdout);
    return 0;
}
```

#### 后记

返回字典序最小的答案卡了我好久

改一下换一个点错。。。

后来才想到要作比较。。。

以后遇到字典序最小还是习惯做一下比较吧。。。

![](https://cdn.luogu.com.cn/upload/pic/37472.png)
