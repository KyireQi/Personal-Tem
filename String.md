# 字符串相关
---

## Manacher
算法应用：求一个字符串的**最长回文字串**，同时这一类问题可以使用**Hash**求解。
```cpp
char s[maxn];
char s_new[2 * maxn];
int p[2 * maxn];

int init()
{
    int len = strlen(s);
    s_new[0] = '$';
    s_new[1] = '#';
    int j = 2;
    for(int i = 0;i < len;i++){
        s_new[j++] = s[i];
        s_new[j++] = '#';
    }
    s_new[j] = '\0';
    return j;
}

int manacher()
{
    int len = init();
    int max_len = 1,id,mx = 0;
    for(int i = 1;i <= len;i++){
        if(i < mx) p[i] = min(p[2 * id - i],mx - i);
        else p[i] = 1;
        while(s_new[i - p[i]] == s_new[i + p[i]]) p[i] ++;
        if(mx < i + p[i]){
            id = i;
            mx = i + p[i];
        }
        max_len = max(max_len,p[i] - 1);
    }
    return max_len;
}

```
## 后缀数组
可以使用Hash + 二分 + 快排的思维去手写，时间复杂度$O(nlog^2{n})$
**例题：**[Ac.Wing 140 后缀数组](https://www.acwing.com/problem/content/description/142/)
一般的做法是倍增或者是`DC3`算法实现，时间复杂度同样还是$O(nlog^2{n})$
```cpp
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>

using namespace std;

const int N = 1000010;

char s[N];
int n, w, sa[N], rk[N << 1], oldrk[N << 1];
// 为了防止访问 rk[i+w] 导致数组越界，开两倍数组。
// 当然也可以在访问前判断是否越界，但直接开两倍数组方便一些。

int main() {
  int i, p;

  scanf("%s", s + 1);
  n = strlen(s + 1);
  for (i = 1; i <= n; ++i) sa[i] = i, rk[i] = s[i];

  for (w = 1; w < n; w <<= 1) {
    sort(sa + 1, sa + n + 1, [](int x, int y) {
      return rk[x] == rk[y] ? rk[x + w] < rk[y + w] : rk[x] < rk[y];
    });  // 这里用到了 lambda
    memcpy(oldrk, rk, sizeof(rk));
    // 由于计算 rk 的时候原来的 rk 会被覆盖，要先复制一份
    for (p = 0, i = 1; i <= n; ++i) {
      if (oldrk[sa[i]] == oldrk[sa[i - 1]] &&
          oldrk[sa[i] + w] == oldrk[sa[i - 1] + w]) {
        rk[sa[i]] = p;
      } else {
        rk[sa[i]] = ++p;
      }  // 若两个子串相同，它们对应的 rk 也需要相同，所以要去重
    }
  }

  for (i = 1; i <= n; ++i) printf("%d ", sa[i]);

  return 0;
}
```
最后的到的$Sa$数组就是存放了按字典序排好的后缀数组的编号

## Kmp算法

**Kmp算法**又称字符串匹配算法，目的是判断A是否是B的字串，同时判断A在B中出现的位置。时间复杂度可以降低到$O(N + M)$

```cpp
void Kmp()
{
    next[1] = 0;
    for(int i = 2,j = 0;i <= n;i++) {
      while(j > 0 && a[i] != a[j + 1]) j = next[j];
      if(a[i] == a[j + 1]) j++;
      next[i] = j;
    }

    for(int i = 1,j = 0;i <= m;i++){
      while(j > 0 && (j == n || b[i] != a[j + 1])) j = next[j];
      f[i] = j;
      //if(f[i] == n) 此时就是A在B中的某一处出现了
    }
}
```
## Trie树（字典树or26叉树）

Trie树是一种用于实现字符串快速检索的多叉树结构。他的**字符数据体现在边上**，节点只是用来记录一些额外信息，比如字符串的结尾标记等。

```cpp
int trie[Size][26],end[Size],tot = 0; //我们假设只有a~z
void insert(char * str) //插入一个字符串
{
    int len = strlen(str),p = 1;
    for(int k = 0;k < len,k++){
        int ch = str[k] - 'a';
        if(trie[p][ch] == 0) trie[p][ch] = ++tot; //给字符上编号
        p = trie[p][ch]; //指针位置改变，变化到下一个位置上。
    }
    end[p] = true;
}

bool search(char * str) //搜索一个字符串
{
    int len = strlen(str),p = 1; //这里我们都是使用p来代表的trie树的指针，初始时指向树的根节点
    for(int k = 0;k < len,k++){
        p = trie[p][str[k] - 'a'];
        if(p == 0) return false;
    }
    return true;
}
```
如果当前给的数据是整形，我们可以把他们理解成是**32位的二进制表示**，这样子我们就可以用Trie树来存储
基本就是下面的代码：题目中要求我们在N个数中找到两个数的异或值最大，下面的是AC代码
```cpp
typedef long long ll;

int trie[32 * maxn][3] = {},tot = 1;

void insert(ll x)
{
    int p = 1;
    for(int k = 31;k >= 0;k--){
        int ch = x >> k & 1;
        if(trie[p][ch] == 0) trie[p][ch] = ++tot;
        p = trie[p][ch];
    }
}

ll Search(ll x)
{
    int p = 1;
    ll ans = 0;
    for(int k = 31;k >= 0;k--){
        int ch = x >> k & 1;
        if(trie[p][ch ^ 1]){
            p = trie[p][ch ^ 1];
            ans |= 1 << k;
        }
        else p = trie[p][ch];
    }
    return ans;
}
```
