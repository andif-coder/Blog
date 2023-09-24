---
title: AtCoder Regular Contest 165
date: 2023-09-19 00:25:05
categories:
- 算法
tags: 
- 算法
- Atcoder
katex: true
---

# B - Sliding Window Sort 2

## 题意

给你长度为$n$的序列$p$，$[1, n]$恰好在序列中各出现一次，现在让你从中选出一个长度为$k$的子数组，

然后对这个子数组从小到大排序后，替换原来的子数组，

这个操作执行一次后，问你字典序最大的$p$是什么样的

## 数据范围

$1 \leq k \leq n \leq 2 \times 10 ^ 5$

$1 \leq p_i \leq n$

## 思路

因为排序后的子数组字典序 $\leq$ 原来的子数组字典序

所以我们最好找到排序后的字典序等于原来字典序的，这样就可以不变了

假设找不到的话，那么我们肯定得让数字发生变换的位置越靠后越好, 这样字典序减少的越少

因为我们找不到排序前后的字典序相同的情况，所以长度为$k$的子数组排序至少有一个位置会发生变化，

我们先假设比较优的解：排序最后$k$个数字，设此时第一个变化的位置为$change_{pos}$

这种情况第一个变化的位置一定是最优的（因为你已经是排序**最后$k$个**了

那么什么情况下会比这种情况还优呢？

那就是第一个变化位置不变的情况下，后面的数字字典序尽量不变，

那么也就是我们把这个子数组的左边界，一直尝试往左边移动，然后第一次变化的位置还是一直不变

那么怎么往左移的过程中，怎么保证第一次变化的位置一直不变呢？

- 左边界到$change_{pos}$是递减的（如果这个不满足的话就break，因为更左边也不可能满足
- $change_{pos}$到右边界的最小值是小于当前左边界的（这个不满足还可以往左，因为你每次会剔除最右边的一个数字

## 代码
```c++
/**
 *    author: andif
 *    created: 17.09.2023 20:04:39
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
    int n, k; cin >> n >> k;
    vi p(n);
    rep(i, 0, n) cin >> p[i];
    vi f(n);
    f[0] = 1;
    rep(i, 1, n) {
        if (p[i] > p[i - 1]) f[i] = f[i - 1] + 1;
        else f[i] = 1;
    }
    bool ok = false;
    rep(i, 0, n) ok |= (f[i] >= k);
    if (!ok) {
        vi a = p;
        sort(a.begin() + n - k, a.begin() + n);
        int change_pos = n - k;
        rep(i, n - k, n) {
            if (a[i] == p[i]) continue;
            change_pos = i;
            break;
        }
        int l = -1, r = n - 1;
        rep(i, change_pos + 1, n) {
            if (p[i] < p[change_pos]) {
                l = i;
                break;
            }
        }
        vi mn(n);
        mn[change_pos] = p[change_pos];
        rep(i, change_pos + 1, n) mn[i] = min(mn[i - 1], p[i]);
        l = l - k + 1, r = r - k + 1;
        l = min(0, l);
        int ans = r;
        per(i, r - 1, l - 1) {
            if (p[i + 1] < p[i]) break;
            int d = change_pos - i;
            int j = change_pos + k - 1 - d;
            if (mn[j] > p[i]) ans = i;
        }
        // de(ans);
        sort(p.begin() + ans, p.begin() + ans + k);
    }
    rep(i, 0, n) {
        cout << p[i];
        if (i == n - 1) cout << '\n';
        else cout << ' ';
    }
    return 0;
}

/*
9 7
8 2 7 3 4 5 6 1 9
*/
```

# C - Social Distance on Graph

## 题意

给你一个无向简单图($n$个点，$m$条边)，该图是连通的，每个边有权重$w_i$，现在让你把每个点染成红色或者蓝色，并且同个颜色点之间简单路径的边权重和最小值最大，让你求这个值$X$ 

## 数据范围

$3 \leq n \leq 2 \times 10^5$

$n - 1 \leq m \leq min(\frac{n(n-1)}{2}, 2 \times 10 ^ 5)$

$1 \leq w_i \leq 10^9$

## 思路

不然发现，这个值$X$具有二分性质，因为大的成立的话，小的肯定成立。

因此我们可以二分$X$，那么我们如何判断一个$X$是否能够成立呢

首先，边权大于$X$的边我们都可以不考虑，

我们就基于边权小于$X$的边来建图，不然发现这个图一定要是个二分图，

所以我们可以通过染色来判断这个图是不是一个二分图，

在这个基础上，我们还得看看含有两个边的简单路径是不是合法，如果合法的话，则所有的简单路径都合法

## 代码
```c++
/**
 *    author: andif
 *    created: 24.09.2023 12:41:10
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

struct Edges {
    int u, v, w;
};

bool dfs(int u, const vector<vector<pii>>& g, vi& vis, int color) {
    if (vis[u] != -1) return vis[u] == color;
    vis[u] = color;
    bool ret = true;
    for (auto [w, v]: g[u]) {
        int nxt_color = vis[u] ^ 1;
        ret &= dfs(v, g, vis, nxt_color);
    }
    return ret;
}

int main() {
    qc;
    int n, m; cin >> n >> m;
    vector<Edges> edges(m);
    rep(i, 0, m) {
        cin >> edges[i].u >> edges[i].v >> edges[i].w;
        edges[i].u--;
        edges[i].v--;
    }

    auto ok = [&] (int x) {
        vector<vector<pii>> g(n);
        for (auto [u, v, w]: edges) {
            if (w >= x) continue;
            // dd(u), dd(v), dd(w), de(x);
            g[u].eb(pair(w, v));
            g[v].eb(pair(w, u));
        }
        rep(i, 0, n) sort(all(g[i]));
        vi vis(n, -1);
        rep(i, 0, n) {
            if (vis[i] != -1) continue;
            if (!dfs(i, g, vis, 0)) {
                // de(i);
                // rep(i, 0, n) dd(i), de(vis[i]);
                return false;
            }
        }

        int mn = std::numeric_limits<int>::max();
        rep(i, 0, n) {
            if (sz(g[i]) < 2) continue;
            mn = min(mn, g[i][0].fi + g[i][1].fi);
        }
        // de(mn);
        return mn >= x;
    };
    int l = 1, r = 2e9;
    while(l + 1 < r) {
        int mid = (1ll * l + r) >> 1;
        if (ok(mid)) l = mid;
        else r = mid;
    }
    cout << (ok(r) ? r : l) << '\n';
    return 0;
}
```


