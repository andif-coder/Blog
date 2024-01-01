---
title: Codeforces Gym 103389
date: 2024-01-01 13:49:08
categories:
- 算法
tags: 
- 状压DP
- Codeforces
katex: true
---

# 连锁商店

## 题意

给你$n$个景点，景点的高度从低到高，然后有$m$个缆车，每个缆车只会从低连向高的景点，然后第$i$个景点属于第$c[i]$公司，第$i$个公司会给旅客一个红包$w[i]$，然后问你从第$1$个景点到达第$i$个景点能收到的最大红包是多少？

## 数据范围

$2 \leq n \leq 36$

$1 \leq m \leq \frac{n(n-1)}{2}$

$1 \leq c_i \leq n$

$1 \leq w_i \leq 10^6$

## 思路

如果按照正常旅行商的状压DP来做，$f[i][j]$表示经过了状态为$i$的景点，现在在第$j$个节点的最大红包，

但是你发现$n$最大值是$36$的，那么$2 ^ {36}$显然不合理，

不难发现，我们不关心经过的景点，只关心经过的公司（因为同一个公司最多只能领取一次红包）

那我们考虑公司，如果是这个公司只有一个景点，那么我直接取就可，如果这个公司包含大于等于$2$个景点的话，我们就把它加入状态里面，这样状态最多只有$2^{18}$

## 注意

- dfs好像会比直接dp来得慢（不清楚为啥

- continue在越顶层的for越好

## 代码
```c++
/**
 *    author: andif
 *    created: 31.12.2023 17:32:09
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
    int n, m; cin >> n >> m;
    vi c(n), w(n), cnt(n);
    rep(i, 0, n) {
        cin >> c[i];
        c[i]--;
        cnt[c[i]]++;
    }
    vi id_2, id(n, -1);
    rep(i, 0, n) {
        if (id[c[i]] != -1) continue;
        if (cnt[c[i]] > 1) {
            id[c[i]] = sz(id_2);
            id_2.eb(c[i]);
        }
    }
    rep(i, 0, n) cin >> w[i];
    vector<vi> e(n);
    rep(i, 0, m) {
        int u, v; cin >> u >> v;
        u--; v--;
        e[u].eb(v);
    }
    vector<vi> f(1 << sz(id_2), vi(n, 0));
    auto dfs = [&] (auto self, int u, int st, int cw) -> void {
        f[st][u] = max(f[st][u], cw);
        for (auto v: e[u]) {
            // dd(v + 1), de(id[c[v]]);
            int ww = w[c[v]], nst = st;
            if (id[c[v]] != -1) {
                ww = (st & (1 << id[c[v]])) ? 0 : w[c[v]];
                nst = st | (1 << id[c[v]]);
            }
            self(self, v, nst, cw + ww);
        }
    };

    int os = id[c[0]] == -1 ? 0 : (1 << id[c[0]]);
    f[os][0] = w[c[0]];
    rep(u, 0, n - 1) {
        rep(j, 0, (1 << sz(id_2))) {
            if (!f[j][u]) continue;
            for (auto v: e[u]) {
                int ww = w[c[v]], nj = j;
                if (id[c[v]] != -1) {
                    ww = (j & (1 << id[c[v]])) ? 0 : w[c[v]];
                    nj = j | (1 << id[c[v]]);
                }
                f[nj][v] = max(f[nj][v], f[j][u] + ww);
                // dd(u + 1), dd(v + 1), dd(j), dd(nj), dd(f[j][u]), dd(ww), dd(c[v]), dd(id[c[v]]), de(f[nj][v]);
            }
        }
    }
    // dfs(dfs, 0, os, w[c[0]]);

    rep(i, 0, n) {
        int ans = 0;
        rep(j, 0, (1 << sz(id_2))) ans = max(ans, f[j][i]);
        cout << ans << '\n';
    }
    return 0;
}
```
