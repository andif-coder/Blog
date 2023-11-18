---
title: Codeforces 607
date: 2023-11-18 17:19:41
categories:
- 算法
tags: 
- 算法
- Codeforces
katex: true
---

# B - Zuma

## 题意

给你一个长度为$n$的数组，第$i$位的值是$a_i$，你现在可以每次选择一个子数组，这个子数组是回文数组，然后花费$1$的代价把这个子数组去掉

问你把整个数组去掉的最小代价是多少

## 数据范围

$1 \leq n \leq 500$

$1 \leq a_i \leq n$

## 思路

设方程为$f[i][j]$表示去除$[i, j]$的最小代价，

不然发现，如果$a_i == a_j$的话，$f[i][j]$可以从$f[i + 1][j - 1]$直接转移过来，不需要花费额外的代价,

如果$a_i \neq a_j$的话，我们就枚举分界点$k$，然后$f[i][j] = min(f[i][j], f[i][k] + f[k + 1][j]$，就相当于是分成两段独立消除

## 代码
```c++
/**
 *    author: andif
 *    created: 18.11.2023 17:13:46
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
const int inf = 1e9;

int main() {
    qc;
    int n; cin >> n;
    vi a(n + 1);
    rep(i, 1, n + 1) cin >> a[i];
    vector<vi> f(n + 1, vi(n + 1, inf));
    rep(i, 1, n + 1) f[i][i] = 1;
    rep(i, 1, n) {
        if (a[i] == a[i + 1]) f[i][i + 1] = 1;
        else f[i][i + 1] = 2;
    }
    rep(l, 3, n + 1) {
        rep(i, 1, n + 1) {
            int j = i + l - 1;
            if (j > n) break;
            if (a[i] == a[j]) f[i][j] = f[i + 1][j - 1];
            rep(k, i, j) {
                f[i][j] = min(f[i][j], f[i][k] + f[k + 1][j]);
            }
            // dd(i), dd(j), de(f[i][j]);
        }
    }
    cout << f[1][n] << '\n';
    return 0;
}
```
