---
title: Codeforces 1774
date: 2023-10-26 23:21:42
categories:
- 算法
tags: 
- 算法
- Codeforces
katex: true
---

# B - Coloring

## 题意

给你一个数组长度为$n$，让你用$m$种颜色给数组的每个元素染色，

要求第$i$种颜色要用$a[i]$次，并且任意连续长度为$k$的子数组里面的任意颜色要不同

问你能不能找到一个合法方案

## 思路

首先，我们把连续长度$k$的子数组都拿出来，当成一个块

那么每个颜色在一个块里面最多出现$1$次，如果有个颜色的$a[i]$超过了块的数量，肯定不合法

然后有个特殊情况，就是$n \mod k != 0$的时候，最后一块的数量不是 $\frac{n}{k}$，我们单独用数量最多的那些颜色，先把它填满就好

## 代码
```c++
/**
 *    author: andif
 *    created: 26.10.2023 23:09:44
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
        int n, m, k; cin >> n >> m >> k;
        vi a(m);
        rep(i, 0, m) cin >> a[i];
        if (m < k) {
            cout << "No" << '\n';
            continue;
        }
        sort(all(a));
        if (n % k) {
            int d = n % k;
            for (int i = m - 1; d-- && i >= 0; --i) a[i]--;
            sort(all(a));
        }
        int c = n / k;
        cout << (*max_element(all(a)) > c ? "No" : "Yes") << '\n';
    }
    return 0;
}
```
