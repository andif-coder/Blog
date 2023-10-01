---
title: AtCoder Beginner Contest 322
date: 2023-10-01 15:11:58
categories:
- 算法
tags: 
- 算法
- Atcoder
katex: true
---

# D - Polyomino

## 题意

给你$3$个拼图，问你把这三个拼图经过平移或者旋转这两种操作，能不能正好填满$4 \times 4$的网格图

## 思路

要搞清楚旋转的操作，然后暴力枚举即可

我们把一个点设为基点（我代码里面是最上面然后最左边的点作为基点），

假设原来其它点相对于基点的偏移为$(x, y)$，那么把拼图顺时针旋转$90$度的话，他们的新的偏移就变成了$(y, -x)$.

## 代码
```c++
/**
 *    author: andif
 *    created: 30.09.2023 21:21:49
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

vector<pii> get(vector<string> s) {
    vector<pii> ret;
    rep(i, 0, 4) rep(j, 0, 4) {
        if (s[i][j] == '#') {
            ret.push_back(pair(i, j));
        }
    }
    rep(i, 1, sz(ret)) {
        auto [x, y] = ret[i];
        auto [x0, y0] = ret[0];
        int dx = x - x0, dy = y - y0;
        ret[i] = pair(dx, dy);
    }
    ret[0] = pair(0, 0);
    return ret;
}

vector<pii> change(vector<pii> a, int c) {
    auto ret = a;
    while(c--) {
        rep(i, 1, sz(ret)) {
            auto [x, y] = ret[i];
            int nx = y, ny = -x;
            ret[i] = pair(nx, ny);
        }
    }
    return ret;
}

bool check(int i, int j, int k, vector<vector<pii>>& A) {
    int xi = i / 4, yi = i % 4;
    int xj = j / 4, yj = j % 4;
    int xk = k / 4, yk = k % 4;
    auto get_real = [&](vector<pii> a, int x, int y) {
        vector<pii> ret(sz(a));
        rep(i, 0, sz(ret)) {
            ret[i].fi = a[i].fi + x;
            ret[i].se = a[i].se + y;
        }
        return ret;
    };
    auto color = [&] (vector<vi>& d, vector<pii> a, int c) {
        for(auto [x, y]: a) {
            if (x < 0 || x > 3 || y < 0 || y > 3) return false;
            if (d[x][y] != -1) return false;
            d[x][y] = c;
        }
        return true;
    };
    rep(h1, 0, 4) {
        auto a = change(A[0], h1);
        rep(h2, 0, 4) {
            auto b = change(A[1], h2);
            rep(h3, 0, 4) {
                auto c = change(A[2], h3);
                auto aa = get_real(a, xi, yi);
                auto bb = get_real(b, xj, yj);
                auto cc = get_real(c, xk, yk);
                vector<vi> d(4, vi(4, -1));
                if (!color(d, aa, 0)) continue;
                if (!color(d, bb, 1)) continue;
                if (!color(d, cc, 2)) continue;
                bool ok = true;
                rep(i, 0, 4) rep(j, 0, 4) if (d[i][j] == -1) ok = false;
                if (ok) return true;
            }
        }
    }
    return false;
}

int main() {
    vector<vector<string>> s(3, vector<string>(4));
    rep(i, 0, 3) rep(j, 0, 4) cin >> s[i][j];
    vector<vector<pii>> a(3);
    rep(i, 0, 3) a[i] = get(s[i]);
    bool ans = false;
    rep(i, 0, 16) rep(j, 0, 16) rep(k, 0, 16) {
        if (i == j || j == k || i == k) continue;
        if (check(i, j, k, a)) ans = true;
    }
    cout << (ans ? "Yes" : "No") << '\n';
    return 0;
}
```
