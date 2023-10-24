---
title: Codeforces 898
date: 2023-10-25 00:05:21
categories:
- 算法
tags: 
- 算法
- Codeforces
katex: true
---

# G - ABBC or BACB

## 题意

给你一个只包含$A$和$B$字符的字符串，你每次可以把$AB$变成$BC$，$BA$变成$CB$，问你最多可以操作多少次

## 数据范围

$1 \leq t \leq 1000$

$\sum |s| \leq 2 \times 10^5$

## 思路

可以发现，如果$B$开头或者$B$结尾的所有$A$都可以被操作掉

那么就只剩下$A$开头并且$A$结尾的情况，

这种情况如果出现连续的两个$B$的话，我们也可以把这个字符串，拆成两个字符串，这两个字符串也都可以通吃

以上三种情况，$A$都可以被消灭

但是如果$A$开头并且$A$结尾，并且没有连续的$B$的话，我们只能牺牲一段长度最小的连续$A$了

## 代码
```c++
/**
 *    author: andif
 *    created: 24.10.2023 23:06:13
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
        string s; cin >> s;
        int n = sz(s);
        vi a;
        int c = 0;
        rep(i, 0, n) {
            if (s[i] == 'B') {
                if (c) a.push_back(c);
                c = 0;
            } else c++;
        }
        if (c) a.push_back(c);
        if (!sz(a)) {
            cout << "0" << '\n';
            continue;
        }
        sort(all(a));
        bool ok = s[0] == 'B' || s[n - 1] == 'B';
        rep(i, 0, n - 1) if(s[i] == 'B' && s[i + 1] == 'B') ok = true;
        int sum = accumulate(all(a), 0);
        int ans = ok ? sum : sum - a[0];
        cout << ans << '\n';
    }
    return 0;
}
```
