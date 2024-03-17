---
title: BZOJ B3687
date: 2024-03-17 22:10:13
categories:
- 算法
tags: 
- bitset
- 洛谷
katex: true
---

# 简单题

## 题意

给你一个大小为$n$的集合，集合的元素为$a_i$，问你所有子集和的异或和为多少

## 数据范围

$1 \leq n \leq 1000$

$\sum a_i \leq 2 \times 10 ^ 6$

## 思路

$f[i][j]$表示前$i$个元素组成和为$j$的子集数量，

不难发现这个只要求解奇偶即可，然后所有$f[n][j] = 1$的$j$异或起来就是答案，

方程的转移为$f[i][j] = f[i - 1][j]$ ^ $f[i - 1][j - a_i]$

不难发现这个复杂度为$O(n \times \sum a_i)$，差不多$2 \times 10 ^ 9$

我们把$f[i]$想象成一个bool数组，

如果$f[i][j]$ ^ $f[i][j]$是不是就相当于$f[i]$这个bool数组每一位异或

那么$f[i][j]$ ^ $f[i][j - x]$是不是就相当于$f[i]$这个数组左移$x$位然后再异或上它自己（左移是因为bitset的最高位是在左边）

## 代码
```c++
/**
 *    author: andif
 *    created: 17.03.2024 22:08:56
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
const int N = 2e6 + 6;

bitset<N> f;
int main() {
    qc;
    int n; cin >> n;
    f[0] = 1;
    rep(i, 0, n) {
        int x; cin >> x;
        f = f ^ (f << x);
    }
    int ans = 0;
    rep(i, 1, N) {
        if (f[i]) ans ^= i;
    }
    cout << ans << '\n';
    return 0;
}
```




