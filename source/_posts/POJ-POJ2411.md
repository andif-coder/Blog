---
title: POJ POJ2411
date: 2023-12-30 17:29:05
categories:
- 算法
tags: 
- 状压DP
- POJ
katex: true
---

# Mondriaan's Dream

## 题意

给你一个矩形$n \times m$，然后给你无数个$1 \times 2$的小矩形,问你有多少种方案可以铺满？

## 数据范围

$1 \leq n, m \leq 11$

## 思路

状态压缩DP, $f[i][j][k]$表示枚举完了$(i, j)$，轮廓线的状态为$k$的情况，如果$k = 10011$，为$1$的位，就表示对应那一列原先放置的东西给占住了，如果为$0$就表示没有被原来放置的东西占住，那么最终答案就是$f[n][m][0]$

![轮廓线](/images/lunkuoxian.png)



## 代码
```c++
/**
 *    author: andif
 *    created: 30.12.2023 16:53:28
**/
#include<iostream>
#include<vector>
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
typedef long long ll;
typedef vector<ll> vl;

ll solve(int n, int m) {
    vector<vector<vl> > f(n + 1, vector<vl>(m + 1, vl((1 << m), 0)));
    f[0][m][0] = 1;
    rep(i, 1, n + 1) {
        rep(j, 1, m + 1) {
            int pj = j - 1 ? j - 1 : m;
            int pi = j - 1 ? i : i - 1;
            rep(pk, 0, (1 << m)) {
                if (f[pi][pj][pk]) {
                    // 如果(i, j)已经被放了
                    if (pk & (1 << (j - 1))) {
                        int nk = pk ^ (1 << (j - 1));
                        f[i][j][nk] += f[pi][pj][pk];
                    } else {
                        // 横着放
                        if (!(pk & (1 << j)) && j != m) {
                            int nk = pk | (1 << j);
                            f[i][j][nk] += f[pi][pj][pk];
                        }
                        // 竖着放
                        if (i != n) {
                            int nk = pk | (1 << (j - 1));
                            f[i][j][nk] += f[pi][pj][pk];
                        }
                    }
                }
            }
        }
    }
    return f[n][m][0];
}

int main() {
    qc;
    while(true) {
        int n, m; cin >> n >> m;
        if (!n && !m) break;
        cout << solve(n, m) << '\n';
    }
    return 0;
}
```
