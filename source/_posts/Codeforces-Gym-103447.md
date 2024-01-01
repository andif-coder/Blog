---
title: Codeforces Gym 103447
date: 2024-01-01 16:22:06
categories:
- 算法
tags: 
- 状压DP
- Codeforces
katex: true
---

# G.Damaged Bicycle

## 题意

给你$n$个点，$m$个无向边的图，让你从$1$到$n$，我们知道人的速度为$t$，自行车的速度为$r$，如果一个点没有自行车，那么你就只能步行，如果一个点有自行车的话，并且自行车是好的状态下，你就会直接骑自行车到$n$，问你最少花费的期望时间。

有$k$个点有自行车，第$i$个自行车坏的概率是$p[i]$。

## 数据范围

$1 \leq t \leq r \leq 10^4$

$1 \leq n, m \leq 10^5$

$1 \leq u_i, v_i \leq n, u_i \neq v_i$

$1 \leq w_i \leq 10^4$

$0 \leq k \leq 18$

$1 \leq a_i \leq n$

$0 \leq p_i \leq 100$

## 思路

我们知道从某个点走去终点的方式其实就只有下面的情况:

- 如果自行车是好的
    - 直接起自行车去终点

- 如果没有自行车，或者自行车坏了
    - 步行去终点
    - 继续去找下一个自行车点碰碰运气

那么我们其实就关心有自行车的点就行了，我们定义状态$f[i][j]$表示经过的自行车点状态为$i$，现在在第$j$个自行车点，然后从$j$这个点达到终点的最小期望，

那么状态转移就是,
- p_bad * (找某个自行车点 或者 直接步行，两个取最小期望) + (1 - p_pad) * (这个点到终点距离除以自行车速度)

我们知道初始期望，也就是$f[(1 << k) - 1][i]$，就是经过了所有的自行车点，然后最后在$i$这个点，达到终点的期望，

**很重要的一点，这个状态不需要关心前面是怎么经过所有自行车点，然后再达到$i$点！！！！！**

那么既然所有点都试过了，那么我们只能从$i$步行，或者$i$点起自行车了（看概率了），**不可能再去其他自行车点碰碰运气了**

## 改进

下面的代码的initial state其实不用计算，因为如果把for循环从$(1 << k) - 2$改成$(1 << k) - 1$就是已经计算了初始状态了.

## 代码
```c++
/**
 *    author: andif
 *    created: 01.01.2024 14:55:44
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
const int inf = 1 << 30;
priority_queue<pii, vector<pii>, greater<pii>> q;

void dij(int s, const vector<vector<pii>>& e, vi& dis) {
    vi vis(sz(e), 0);
    dis[s] = 0;
    q.push(pair(dis[s], s));
    while(!q.empty()) {
        auto [d, u] = q.top();
        q.pop();
        if (vis[u]) continue;
        vis[u] = 1;
        for (auto& [w, v]: e[u]) {
            if (dis[v] > dis[u] + w) {
                dis[v] = dis[u] + w;
                q.push(pair(dis[v], v));
            }
        }
    }
}

int main() {
    int v1, v2; scanf("%d%d", &v1, &v2);
    int n, m; scanf("%d%d", &n, &m);
    vector<vector<pii>> e(n + 1);
    rep(i, 0, m) {
        int u, v, w; scanf("%d%d%d", &u, &v, &w);
        e[u].eb(w, v);
        e[v].eb(w, u);
    }
    int k; scanf("%d", &k);
    vi a(k), p(k);
    rep(i, 0, k) scanf("%d%d", &a[i], &p[i]);
    a.eb(1); a.eb(n); // begin & end point
    vector<vi> d(sz(a) + 1, vi(sz(a), inf));
    rep(i, 0, k + 2) {
        vi dis(n + 1, inf);
        dij(a[i], e, dis);
        rep(j, 0, k + 2) {
            d[i][j] = dis[a[j]];
            // dd(a[i]), dd(a[j]), de(d[i][j]);
        }
    }

    if (d[k][k + 1] == inf) {
        return printf("-1\n"), 0;
    }

    vector<vector<double>> f(1 << k, vector<double>(k, 0));
    // initial state
    rep(i, 0, k) {
        double p_bad = 1. * p[i] / 100;
        f[(1 << k) - 1][i] = p_bad * d[i][k + 1] / v1 + (1 - p_bad) * d[i][k + 1] / v2;
    }
    // DP
    per(s, (1 << k) - 2, -1) {
        rep(i, 0, k) if (s & (1 << i)) {
            double p_bad = 1. * p[i] / 100;
            // 开车
            double t_good = 1. * d[i][k + 1] / v2;
            // 不开车
            double t_bad = 1. * d[i][k + 1] / v1; // 走路
            rep(j, 0, k) if (!(s & (1 << j))) {
                t_bad = min(t_bad, 1. * d[i][j] / v1 + f[s | (1 << j)][j]);
            }
            f[s][i] = p_bad * t_bad + (1 - p_bad) * t_good;
        }
    }

    double ans = 1. * d[k][k + 1] / v1;;
    rep(i, 0, k) {
        ans = min(ans, 1. * d[k][i] / v1 + f[(1 << i)][i]);
    }
    printf("%.10f\n", ans);
    return 0;
}
```
