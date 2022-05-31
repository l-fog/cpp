## § 欧几里得算法

### 基本知识

#### 最大公约数、最小公倍数

用 `gcd(a,b)` 表示 $a$ 和 $b$ 的最大公约数

用 `lcm(a,b)` 表示 $a$ 和 $b$ 的最小公倍数

> - 定理
>
>   `lcm(a,b)=(a*b)/gcd(a,b)` 

#### 最大公约数的相关性质

1.  $\gcd(a,b)=\gcd(b,a)$ 
2.  $\gcd(a,b)=\gcd(-a,b)$ 
3.  $\gcd(a,b)=\gcd(b,a-b)$ 
4.  $\gcd(a,b)=\gcd(b,a\%b)$ 
5.  $\gcd(a,0)=|a|$ 
6.  $\gcd(a,ka) = |a|$ 
7.  $\gcd(ma,mb)=m\times\gcd(a,b)$ 
8.  $\gcd(a,b) = \gcd(a+mb,b)$
9.  $\gcd(ab,m)=\gcd(a,m)\times\gcd(b,m)$ 
10.  $\gcd(a,p)=1,\gcd(b,p)=1\Rightarrow\gcd(a\times b,p)=1$

### 更相减损法（略）

### Euclidean Algorithm

#### 定义及原理

又称 辗转相除法

原理 $\gcd(a,b)=\gcd(b,a\%b)$ 

#### 用法

```cpp
template<class T>inline T gcd(T a,T b){
    return b==0?a:gcd(b,a%b);
}
```

#### 证明

$$
设 d=\gcd(a,b),d\in \mathbb{Z}^+\notag
\\有 a=md,b=nd,其中m,n\in \mathbb Z^+,且\gcd(m,n)=1\\
\begin{equation}
	\begin{aligned}
	令\ r&=a\%b
	\\\Rightarrow \ r&=a-qb
	\\ &=md-qnd
	\\ &=d(m-qn),(q\in\Z^+)
	\\\because \gcd(b,r) &= \gcd(nd,d(m-qn))
	\\ &=d\times\gcd(n,m-qn)
	\\ &=\gcd(a,b)\times\gcd(n,m-qn)
	\\且\ \gcd(n,m)=1&\Rightarrow\gcd(n,m-qn)=1 
	\\ \therefore\gcd(a,b)&=\gcd(b,a\%b)=d
	\\得证\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ 
	\end{aligned}
\end{equation}
$$

### 裴蜀定理

#### 定义及原理

又名贝祖定理
$$
\forall a,b\in \Z^+,且\ \gcd(a,b)=d
\\ 关于\ x,y\ 的线性不定方程:
\\ ax+by=c
\\ 有整数解\ (x,y)\ ,当且仅当\ c\%d=0
\\ 特别地,一定存在\ x,y\in\Z,使得\ ax+by=d\ 成立 
$$

#### 证明

$$
\begin{align}
	设\ d=\gcd(a,b)\notag
	\\设\ &s\ 是满足等式\ ax+by=s,x,y\in\Z\ 的最小正数\notag
	\\设\ &r=a\%s\Rightarrow r=a-qs,其中q\in\Z\notag\\
	令\ &x'=1-qx,y'=-qy\notag
	\\有\ &r=ax'+by'\notag
	\\\because\ &0\le r\lt s\notag
	\\且\ &s\ 是满足等式\ ax+by=s,x,y\in\Z\ 的最小正数\notag
	\\\Rightarrow\ &r=0\notag
	\\\therefore\ &a\%s=0\notag
	\\同理\ &b\%s=0\notag
	\\\therefore\ &s是a,b的一个公约数\notag
	\\\therefore\ &d=ks,k\in\Z^+\notag
	\\\because\ &不定方程ax+by=s有整数解(x,y)\notag
	\\\therefore\ &不定方程一定有整数解(kx,ky)\notag
	\\ 得证\notag
\end{align}
$$

### 扩展欧几里得

#### 定义

已知 $a,b$ ，扩展欧几里得算法可以求出 $\gcd(a,b),以及x,y\in\Z$ ，满足 $ax+by=\gcd(a,b)$ 

#### 用法

```cpp
template<class T>inline T exgcd(T a,T b,T&x,T&y){
    if(b==0){
        x=1,y=0;return a;
    }
    T gcd=exgcd(b,a%b,y,x);
    y=y-a/b*x;
    return gcd;
}
```

#### 解释

$$
\begin{align}
	\text i)\ b=0时\notag
	\\ &\gcd(a,b)=a\notag
	\\ 方程可化为\ \ &a\times x+0\times y=a\notag
	\\\therefore\ &x=1,y=0\ 是这个方程的一组解\notag
	\\\notag
	\\\text{ii})\ b>0时\notag
	\\\because\ &\gcd(a,b)=\gcd(b,a\%b)\notag
	\\\therefore\ &可以列出以下等式\notag
	\\ &ax_1+by_1=\gcd(a,b)\tag{1}
	\\&bx_2+(a\%b)y_2=\gcd(b,a\%b)\tag{2}
	\\\because\ &a\%b=a-b\lfloor\frac a b\rfloor\notag
	\\且\ \ &\gcd(a,b)=\gcd(b,a\%b)\notag
	\\\therefore\ &ax_1+by_1=bx_2+(a-b\lfloor\frac a b\rfloor)y_2\notag
	\\&=ay_2+b(x_2-y_2\lfloor\frac a b\rfloor)\notag
	\\\therefore\ &x_1=y_2,y_1=x_2-y_2\lfloor\frac a b\rfloor\notag
\end{align}
$$

由上面的推导可以知道：

当递归达到边界条件时 `(b==0)` ，$x=1,y=0$ 是目标方程的一组解 $(x_2,y_2)$ 

在向上返回的过程中，对应方程的解也会发生改变

若上一层返回的解为 $(x_2,y_2)$ ，则本层对应的解为 $(y_2,x_2-y_2\lfloor\frac a b \rfloor)$ 

那么在递归的过程中使用 `exgcd(b,a%b,y,x)` 就可以让 `x` 直接获得解，然后只需要计算 `y` 的值就好了

在 `C++` 中，整数除法默认向下取整，故可以写成 `y=y-a/b*x` 的形式

根据递归求解的正确性，可以确定递归结果是正确的

${\huge\color{red}{注意，这里的x与y 的正负是未知的}}$ 

#### 两个定理

定理一：对于不定方程 $ax+by=c,c\%\gcd(a,b)=0$ ，一定存在整数解为 $(\frac c {\gcd(a,b)}x,\frac c {\gcd(a,b)}y)$ ~~(这个应该没必要证明了吧)~~

定理二：若 $(x,y)$ 是不定方程 $ax+by=\gcd(a,b)$ 的一组整数解，则 $(x+k\frac b {\gcd(a,b)},y-k\frac a {\gcd(a,b)})$ 也是方程的一组整数解
$$
\begin{align}
	证明\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \notag \\
	设\ d=\gcd(a,b)&\notag\\\notag
	设\ \ (x_1,y_1)和(x_2&,y_2)均为方程\ ax+by=d的整数解,则\\\notag
	&ax_1+by_1=d\tag{1}\\\notag
	&ax_2+by_2=d\tag{2}\\\notag
	(1)-(2)\Rightarrow\ &a(x_1-x_2)+b(y_1-y_2)=0\\\notag
	\Rightarrow\ &\frac a d(x_1,x_2)=-\frac b d(y1-y2) \\\notag
	\because\ &d=\gcd(a,b)\\\notag
	\therefore\ &\gcd(\frac a d,\frac b d)=1\\\notag
	\therefore\ &\frac b d|(x_1-x_2)\\
	\therefore\ &x_1-x_2=k\frac b d\Rightarrow x_1=x_2+k\frac b d\tag{3}\\\notag
	联立(1),(3),有\ &a(x_2-k\frac b d)+by_1=d\\
	\Rightarrow\ &ax_2+k\frac{ab} d+by_1=d\tag{4}\\\notag
	联立(2),(4),有\ &d-by_2+k\frac{ab} d +by_1=d\\\notag
	\therefore\ &y_1=y_2-k\frac a d\\\notag
	得证\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ 
\end{align}
$$

### 应用扩展欧几里得求乘法逆元

#### 定义

对于 $ax\equiv 1 \left(\text{mod}\ p\right)$ ，$x为a在模p意义下的逆元$ 

换言之，对于方程 $ax+by=1,\gcd(a,b)=1$ ， $x\in\left[1,b\right)$ 是 $a$ 在模 $b$ 意义下的逆元

#### 用法

那么我们就可以用 `exgcd` 求解这个方程，这样就得到了逆元

```cpp
//扩展欧几里得
template<class T>inline T gcd(T a,T b,T&x,T&y){
    if(b==0){
        x=1,y=0;return a;
    }
    T g=gcd(b,a%b,y,x);
    y=y-a/b*x;
    return g;
}
//求 a 在模 p 意义下的逆元
inline int sol(int a,int p){
    int x,y;
    int g=gcd(a,p,x,y);
    int ans=x,t=0;
    //确保 x in [1,p)
    while((ans=x+b/g*t)<1)t++;
    while((ans=x+b/g*t)>=p)t--;
    return ans;
}
```

这里用到了一个定理：

> 定理二：若 $(x,y)$ 是不定方程 $ax+by=\gcd(a,b)$ 的一组整数解，则 $(x+k\frac b {\gcd(a,b)},y-k\frac a {\gcd(a,b)})$ 也是方程的一组整数解

作用是确保 $x\in\left[1,p\right)$ 

#### e.g.

> 给定 $n$ 个正整数 $a_i$ ， $p=10^9+7$ ，求
> $$
> \sum_{i=1}^{n}{(a_i^{-1}\times 998244353^{n-i})(\text{mod}\ p)}
> $$

$$
\begin{align}\\\notag
	解:\ \ \  &\ \\\notag
	由\ &ax\equiv1(\text{mod}\ p)知:\\\notag
	&x=\frac 1 a (\text{mod}\ p)\\\notag
	设\ &\{a_n\} \ 是题目所给出的数组\\\notag
	则\ &其逆元可表示为:\{\frac 1 {a_n}\}\\\notag
	设\ &\{s_i\}中s_i=a_1\times a_2\times\dots\times a_i\\
	则\ &s_i=s_{i-1}\times a_i\tag{1}\\\notag
	\therefore\ &\frac 1 {s_i}=\frac 1 {s_{i-1}\times a_i}\\
	\therefore\ &\frac 1 {s_{i-1}}=\frac 1 {s_i}\times a_i\tag{2}\\
	\therefore\ &\frac 1 {a_i}=\frac {s_{i-1}} {s_i}\tag{3}\\\notag
	\because\ &a_i已知,s_i可以线性推导\\\notag
	\therefore\ &可以线性推导出\frac 1 {s_n}\\\notag
	\therefore\ &可以再次线性推导出\frac 1 {s_i},进而求出\frac 1 {a_i}\notag
\end{align}
$$

```cpp
#include<bits/stdc++.h>
using namespace std;
#define GO(u,v,i) for(int i=u;i<=v;i++)
template<class t>inline t fr(){
    register t num=0,dis=1;
    register char ch=getchar();
    while(ch<'0'||ch>'9'){if(ch=='-')dis=-1;ch=getchar();}
    while(ch>='0'&&ch<='9'){num=(num<<1)+(num<<3)+(ch^48);ch=getchar();}
    return num*dis;
}
template<class t>inline void fw(t num){
    if(num>9)fw(num/10);
    putchar(num%10+'0');
}
template<class t>inline void fw(t num,char ch){
    if(num<0)num=-num,putchar('-');
    fw(num);putchar(ch);
}
const int maxn=5e6+12;
typedef long long lld;
const lld p=1e9+7,k=998244353;
int n;
lld a[maxn],s[maxn],sns[maxn],an[maxn];
lld anss;
template<class t>inline t gcd(t a,t b,t&x,t&y){
    if(b==0){
        x=1,y=0;
        return a;
    }
    t g=gcd(b,a%b,y,x);
    y=y-a/b*x;
    return g;
}
template<class t>inline t power(t num,t tim){
    t ans=1;
    GO(1,tim,i)ans*=num;
    return ans;
}
inline void sol(){
    n=fr<int>();
    s[0]=1;//初始化
    GO(1,n,i){
        a[i]=fr<lld>();
        s[i]=(s[i-1]*a[i])%p;//根据公式(1)
    }
    lld x,y;
    lld g=gcd(s[n],p,x,y);
    lld ans=x,t=0;
    while((ans=x+p/g*t)<1)t++;
    while((ans=x+p/g*t)>=p)t--;
    sns[n]=ans;// 1/bn 已求出
    lld tmp=1;
    for(int i=n;i>0;i--){
        sns[i-1]=(sns[i]*a[i])%p;//根据公式(2)
        an[i]=(sns[i]*s[i-1])%p;//根据公式(3)
        anss+=(an[i]*tmp)%p;//题目要求
        tmp*=k;tmp%=p;//tmp就是k^(n-i)
    }
    fw(anss%p,'\n');
}
signed main(){
    sol();
    return 0;
}
```
