# 组合数学

## 组合数的计算
下面是**带模**的组合数计算
```cpp
ll f[maxn];

ll qpow(ll a, ll b) {
    ll ans = 1, base = a;
    while (b) {
        if (b & 1) ans = ans * base % mode;
        base = base * base % mode;
        b >>= 1;
    }
    return ans;
}

void init() {
    f[0] = 1;
    for (int i = 1;i <= 2e5;i++) {
        f[i] = f[i - 1] * i % mode;
    }
}

ll calc(ll n, ll m) {
    if (n < m) return 0;
    return 1ll * f[n] * qpow(f[m], mode - 2) % mode * qpow(f[n - m], mode - 2) % mode;
}
```