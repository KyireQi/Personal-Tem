# 数论知识

## 埃式筛
可以实现$O(n)$复杂度的筛子
```cpp
int m;
int v[maxn],prime[maxn],vis[maxn];
void Prime(int n)
{
    memset(v,0,sizeof(v));
    m = 0;
    for(int i = 2;i <= n;i++) {
        if(v[i] == 0) {
            v[i]s = i;
            prime[++m] = i;
            vis[i] = 1;
        }
        for(int j = 1;j <= m;j ++) {
            if(prime[j] > v[i] || prime[j] > n / i) break;
            v[i * prime[j]] = prime[j];
        }
    }
}
```