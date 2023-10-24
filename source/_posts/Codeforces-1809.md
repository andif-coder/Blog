---
title: Codeforces 1809
date: 2023-10-25 00:49:32
categories:
- 算法
tags: 
- 算法
- Codeforces
katex: true
---

# C - Sum on Subarrays

## 题意

给你两个数$n$, $k$，

让你构造一个长度为$n$的数组，满足下面两个条件

- $k$个子数组满足和为正
- $\frac{(n + 1) * n}{2} - k$个剩余子数组的和为负

## 数据范围

$1 \leq t \leq 5000$

$2 \leq n \leq 30$

$0 \leq k \leq \frac{(n + 1) * n}{2}$

## 思路

我们可以先构造一个所有子数组和都为正数的情况，比如$1, 2, \cdots, n$

接着我们开始把一些数字变成负数，让正数子数组的数量变少，比如第一个数字，如果我们把它变成$-1000$的话，整个正数组的数量就少了$n$，接着就考虑第二个数字，我们可以调整它的大小，让正数子数组的数量接着减少（最多减少$n - 1$)，那么就可以构造所有情况了

## 代码
```c++
/**
 *    author: andif
 *    created: 25.10.2023 00:34:05
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
const int mn = -1000;

int main() {
    qc;
    int t; cin >> t;
    while(t--) {
        int n, k; cin >> n >> k;
        k = (n + 1) * n / 2 - k;
        vi a(n);
        iota(all(a), 1);
        int p = 0, c = n;
        while(k) {
            if (k >= c) a[p] = mn;
            else {
                a[p] = (p + 1 + p + k) * k / 2 - a[p];
                a[p] = -a[p] - 1;
                k = 0;
                break;
            }
            k -= c;
            c--;
            p++;
        }
        rep(i, 0, n) {
            cout << a[i];
            cout << (i == n - 1 ? '\n' : ' ');
        }
    }
    return 0;
}
```
