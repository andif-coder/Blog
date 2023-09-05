---
title: AtCoder Beginner Contest 318
date: 2023-09-05 21:36:00
tags:
katex: true
---

# F - Octopus

## 题意

有个章鱼有$N$个脚，然后同时有$N$个宝藏，宝藏位于一条直线上，第$i$个宝藏位于$X_i$位置，

假设章鱼的头位于$k$，第$i$个脚的长度为$L_i$，那么第$i$个脚可以抓到$k-L_i \leq x \leq k + L_i$范围内的一个宝藏

问你有多少个合法的$k$

## 数据范围

- $1 \leq N \leq 200$
- $-10^{18} \leq X_1 < X_2 < \cdots < X_N \leq 10 ^ {18}$
- $1 \leq L_1 \leq L_2 \leq \cdots \leq L_N \leq 10 ^ {18}$
- 所有输入都是整数

## 思路

![](1.png)

考虑把章鱼头放到$k_0$的位置，那么我们怎么判断这个位置可以不可以呢？

**我们肯定让最短的那个脚，去抓最近的那个宝藏，这样是最优的**

那么其实我们只要考虑每个位置的优先级（也就是具体章鱼头的距离。

那么什么时候会发生优先级变化的情况呢？

**也就是$x_i$和$x_j$的中点，或者$x_i$自己这个点**，这样我们可以根据这些分割点把整个区间变成若干个小区间

这些小区间最多就$N^2$的数量级，我们可以暴力判断这些小区间能不能满足就好

## 代码

```c++
/**
 *    author: andif
 *    created: 03.09.2023 15:37:14
**/
#include<bits/stdc++.h>
using namespace std;

#define de(x) cerr << #x << " = " << x << endl
#define dd(x) cerr << #x << " = " << x << " "
#define rep(i, a, b) for(int i = a; i < b; ++i)
#define per(i, a, b) for(int i = a; i > b; --i)
#define mt(a, b) memset(a, b, sizeof(a))
#define sz(a) (int)a.size()
#define fi first
#define se second
#define qc ios_base::sync_with_stdio(0);cin.tie(0)
#define eb emplace_back
#define all(a) a.begin(), a.end()
using ll = long long;
using db = double;
using pii = pair<int, int>;
using pdd = pair<db, db>;
using pll = pair<ll, ll>;
using vi = vector<int>;
using vl = vector<ll>;
const db eps = 1e-9;

int main() {
    qc;
    int n; cin >> n;
    vl X(n), L(n);
    rep(i, 0, n) cin >> X[i];
    rep(i, 0, n) cin >> L[i];
    ll l = -2e18, r = 2e18;
    ll ans = 0;
    // Left
    rep(i, 0, n) l = max(l, X[i] - L[i]);
    ans += max(0ll, X[0] - l);
    // right
    rep(i, 0, n) r = min(r, X[i] + L[n - i - 1]);
    ans += max(0ll, r - X[n - 1] + 1);
    // between x_i ans x_{i + 1} - 1
    vi id(n);
    iota(all(id), 0);
    rep(i, 0, n - 1) {
        vl d{X[i]};
        rep(j, 0, n) {
            rep(k, j + 1, n) {
                ll mid = (X[j] + X[k] + 1) / 2;
                if (mid > X[i] && mid < X[i + 1]) {
                    d.eb(mid);
                }
            }
        }
        d.eb(X[i + 1]);
        sort(all(d));
        rep(j, 0, sz(d) - 1) {
            vl pos(n);
            rep(k, 0, n) pos[k] = abs(X[k] - d[j]);
            sort(all(id), [&] (int x, int y) {
                if (pos[x] == pos[y]) return y > x;
                return pos[x] < pos[y];
            });
            vi rk(n);
            rep(k, 0, n) rk[id[k]] = k;
            ll l = d[j], r = d[j + 1] - 1;
            rep(k, 0, n) {
                int h = rk[k];
                if (l > r) break;
                ll l1 = X[k] - L[h], r1 = X[k] + L[h];
                l = max(l, l1);
                r = min(r, r1);
            }
            if (l <= r) ans += r - l + 1;
        }
    }
    cout << ans << '\n';
    return 0;
}
```

