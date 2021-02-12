# 数论知识

## 埃式筛
埃式筛有两种，一种**常规筛**，一种**线性筛**。
下面可以实现$O(nloglogn)$复杂度
```cpp
void Prime(int x)
{
    memset(v,0,sizeof(v));
    for(int i = 2;i <= n;i ++) {
        if(v[i]) continue;
        for(int j = i;j <= n / i;j ++ ) v[i * j] = 1;
    }
}
```
下面可以实现$O(n)$复杂度的筛子
通过记录每一个合数的最小质因子来实现对于每一个数只扫描一次。
```cpp
int m;
int v[maxn],prime[maxn],vis[maxn];
void Prime(int n)
{
    memset(v,0,sizeof(v)); //最小质因子
    m = 0; //质数个数
    for(int i = 2;i <= n;i++) {
        if(v[i] == 0) {
            v[i] = i;
            prime[++m] = i;
            vis[i] = 1; //标记
        }
        for(int j = 1;j <= m;j ++) { 
            if(prime[j] > v[i] || prime[j] > n / i) break;
            v[i * prime[j]] = prime[j];
        }
    }
}
```