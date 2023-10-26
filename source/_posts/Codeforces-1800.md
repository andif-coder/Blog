---
title: Codeforces 1800
date: 2023-10-26 22:44:45
categories:
- 算法
tags: 
- 算法
- Codeforces
katex: true
---

# E2 - Unforgivable Curse (hard version)

## 题意

给你两个字符串$s$, $t$，它们的长度都为$n$，

接着给你一个$k$，你可以执行如下操作，

对于两个下标$i$,$j$，如果满足$|i - j| = k$或者$|i - j| = k + 1$，那么就可以交换$s[i]$和$s[j]$

你可以执行任意次，问你有没有办法把$s$变成$t$

## 思路

如果把可以交换的下标，连成一个图，

不难发现，相同连通块的所有字符都可以任意互换

那么我们只要保证$s$和$t$的任意连通块里面的字符都相等即可

## 代码
```c++
/**
 *    author: andif
 *    created: 26.10.2023 22:32:51
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
    int t; cin >> t;
    while(t--) {
        int n, k; cin >> n >> k;
        string s, t; cin >> s >> t;
        vi vis(n);
        auto dfs = [&] (auto self, int u, vi& cs, vi& ct) -> void {
            vis[u] = 1;
            cs[s[u] - 'a']++;
            ct[t[u] - 'a']++;
            vi d{k, -k, k + 1, -k - 1};
            rep(i, 0, 4) {
                int v = u + d[i];
                if (v >= 0 && v < n && !vis[v]) {
                    self(self, v, cs, ct);
                }
            }
        };
        bool ok = true;
        rep(i, 0, n) {
            if (!vis[i]) {
                vi cs(26), ct(26);
                dfs(dfs, i, cs, ct);
                rep(j, 0, 26) ok &= (cs[j] == ct[j]);
            }
        }
        cout << (ok ? "Yes" : "No") << '\n';
    }
    return 0;
}
```
