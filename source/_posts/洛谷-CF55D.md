---
title: 洛谷 CF55D
date: 2023-12-16 19:02:00
categories:
- 算法
tags: 
- 数位DP
- 洛谷
katex: true
---

# Beautiful numbers

## 题意

给你一个区间$[l, r]$，让你求区间里面有多少个数字$x$满足下列性质：

- 对于$x$的每一个非零位$y$，都有$y \mid x$

一共$t$次询问.

## 数据范围

$1 \leq t \leq 10$

$1 \leq l \leq r \leq 9 \times 10 ^ {18}$

## AC之路

请注意只有最后一次的代码才是完全正确的！！！

### 第一次尝试

#### 思路

我们需要知道一个结论，假设$Y \mod X = 0$，那么一个数字$x \mod X = x \mod Y \mod x$.

那么其实我们只需要知道前$i$位对$1 \cdots 9$的最小公倍数（$2520$）的取模值就好

和原来的数位DP做法一样，还是分成$m$类，然后每一类去计算，

$dp[i][j][k]$表示前$i$位对于$2520$的取模为$j$，同时前$i$位数位的最小公倍数为$k$，那么就是$\sum_{j \mod k == 0} dp[m][j][k]$

#### 代码（超时）

```c++
/**
 *    author: andif
 *    created: 16.12.2023 15:37:36
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
const int B = 10;
const int mod = 2520;


vi id(mod + 1, -1), lm;
ll dp[21][mod + 1][50];

int gcd(int a, int b) { return b ? gcd(b, a % b) : a; }

int get_lcm(const vi& a) {
    int ret = 1;
    for (auto x: a) {
        ret = ret / gcd(ret, x) * x;
    }
    return ret;
}

vi get_all_lcm() {
    vi ret;
    set<int> s;
    rep(i, 1, (1 << B)) {
        vi tmp;
        rep(j, 1, B) {
            if ((i >> j) & 1) {
                tmp.eb(j);
            }
        }
        s.insert(get_lcm(tmp));
    }
    for (auto x: s) ret.push_back(x);
    // de(sz(ret));
    return ret;
}

ll gao(ll n) {
    // de(n);
    if (!n) return 0;
    vi c;
    for (; n; n /= 10) c.eb(n % 10);
    c.eb(0);
    reverse(all(c));
    int m = sz(c) - 1;

    ll ret = 0;
    int md = 0, l = 1;
    rep(i, 1, m + 1) {
        if (i == 1) {
            rep(h, i, m + 1) {
                int up = h == 1 ? c[h] : B;
                rep(J, 1, up) {
                    mt(dp, 0);
                    int nmd = (md * 10 + J) % mod;
                    int nl = J ? l / gcd(l, J) * J : l;
                    dp[h][nmd][id[nl]] = 1;
                    // dd(h), dd(nmd), dd(nl), dd(id[nl]), de(dp[h][nmd][id[nl]]);
                    rep(k, h, m) {
                        rep(j, 0, mod) {
                            rep(y, 0, sz(lm)) {
                                if (dp[k][j][y]) {
                                    rep(x, 0, B) {
                                        int nj = (j * 10 + x) % mod;
                                        int ny = x ? (lm[y] / gcd(lm[y], x) * x) : lm[y];
                                        dp[k + 1][nj][id[ny]] += dp[k][j][y];
                                    }
                                }
                            }
                        }
                    }
                    rep(j, 0, mod) {
                        rep(k, 0, sz(lm)) {
                            if (j % lm[k]) continue;
                            // if (j < 10)
                            //     dd(m), dd(j), dd(lm[k]), dd(k), de(dp[m][j][k]);
                            ret += dp[m][j][k];
                        }
                    }
                }
                // dd(i), de(ret);
            }
        } else {
            rep(J, 0, c[i]) {
               mt(dp, 0);
               int nmd = (md * 10 + J) % mod;
               int nl = J ? l / gcd(l, J) * J : l;
               dp[i][nmd][id[nl]] = 1;
               rep(k, i, m) {
                   rep(j, 0, mod) {
                       rep(y, 0, sz(lm)) {
                           if (dp[k][j][y]) {
                               rep(x, 0, B) {
                                   int nj = (j * 10 + x) % mod;
                                   int ny = x ? (lm[y] / gcd(lm[y], x) * x) : lm[y];
                                   dp[k + 1][nj][id[ny]] += dp[k][j][y];
                               }
                           }
                       }
                   }
               }
               rep(j, 0, mod) {
                   rep(k, 0, sz(lm)) {
                       if (j % lm[k]) continue;
                       ret += dp[m][j][k];
                   }
               }
            }
            // dd(i), de(ret);
        }
        md = (md * 10 + c[i]) % mod;
        if (c[i]) {
            l = l / gcd(l, c[i]) * c[i];
        }
    }
    // dd(md), de(l);
    if (md % l == 0) ret++;
    return ret;
}

int main() {
    qc;
    lm = get_all_lcm();
    rep(i, 0, sz(lm)) id[lm[i]] = i;
    int t; cin >> t;
    while(t--) {
        ll l, r; cin >> l >> r;
        // dd(l), de(r);
        // dd(r), de(gao(r));
        cout << gao(r) - gao(l - 1) << '\n';
    }
    return 0;
}
```

### 第二次尝试

#### 思路

基于上面的思路，我们发现上面代码中的$153 \sim 157$就是为了$158 \sim 160$设置动规的初始状态的，我们完全可以把所有初始状态都设置完了，再开始转移

#### 代码(超时)
```c++
/**
 *    author: andif
 *    created: 16.12.2023 15:37:36
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
const int B = 10;
const int mod = 2520;


vi id(mod + 1, -1), lm;
ll dp[21][mod + 1][50];

int gcd(int a, int b) { return b ? gcd(b, a % b) : a; }

int get_lcm(const vi& a) {
    int ret = 1;
    for (auto x: a) {
        ret = ret / gcd(ret, x) * x;
    }
    return ret;
}

vi get_all_lcm() {
    vi ret;
    set<int> s;
    rep(i, 1, (1 << B)) {
        vi tmp;
        rep(j, 1, B) {
            if ((i >> j) & 1) {
                tmp.eb(j);
            }
        }
        s.insert(get_lcm(tmp));
    }
    for (auto x: s) ret.push_back(x);
    // de(sz(ret));
    return ret;
}

ll gao(ll n) {
    // de(n);
    if (!n) return 0;
    vi c;
    for (; n; n /= 10) c.eb(n % 10);
    c.eb(0);
    reverse(all(c));
    int m = sz(c) - 1;

    ll ret = 0;
    int md = 0, l = 1;
    rep(i, 1, m + 1) {
        if (i == 1) {
            rep(h, i, m + 1) {
                int up = h == 1 ? c[h] : B;
                mt(dp, 0);
                rep(J, 1, up) {
                    int nmd = (md * 10 + J) % mod;
                    int nl = J ? l / gcd(l, J) * J : l;
                    dp[h][nmd][id[nl]] = 1;
                }
                rep(k, h, m) {
                    rep(j, 0, mod) {
                        rep(y, 0, sz(lm)) {
                            if (dp[k][j][y]) {
                                rep(x, 0, B) {
                                    int nj = (j * 10 + x) % mod;
                                    int ny = x ? (lm[y] / gcd(lm[y], x) * x) : lm[y];
                                    dp[k + 1][nj][id[ny]] += dp[k][j][y];
                                }
                            }
                        }
                    }
                }
                rep(j, 0, mod) {
                    rep(k, 0, sz(lm)) {
                        if (j % lm[k]) continue;
                        ret += dp[m][j][k];
                    }
                }
            }
        } else {
            mt(dp, 0);
            rep(J, 0, c[i]) {
               int nmd = (md * 10 + J) % mod;
               int nl = J ? l / gcd(l, J) * J : l;
               dp[i][nmd][id[nl]] = 1;
            }
            rep(k, i, m) {
                rep(j, 0, mod) {
                    rep(y, 0, sz(lm)) {
                        if (dp[k][j][y]) {
                            rep(x, 0, B) {
                                int nj = (j * 10 + x) % mod;
                                int ny = x ? (lm[y] / gcd(lm[y], x) * x) : lm[y];
                                dp[k + 1][nj][id[ny]] += dp[k][j][y];
                            }
                        }
                    }
                }
            }
            rep(j, 0, mod) {
                rep(k, 0, sz(lm)) {
                    if (j % lm[k]) continue;
                    ret += dp[m][j][k];
                }
            }
        }
        md = (md * 10 + c[i]) % mod;
        if (c[i]) {
            l = l / gcd(l, c[i]) * c[i];
        }
    }
    if (md % l == 0) ret++;
    return ret;
}

int main() {
    qc;
    lm = get_all_lcm();
    rep(i, 0, sz(lm)) id[lm[i]] = i;
    int t; cin >> t;
    while(t--) {
        ll l, r; cin >> l >> r;
        cout << gao(r) - gao(l - 1) << '\n';
    }
    return 0;
}
```

### 第三次尝试

#### 思路

又学到了数位DP的另外一种写法，原来我的写法是分成$m$类，新的写法是把状态多了一维，

$dp[i][j][k][h]$其中最后一维表示是否前$i$位都和$n$的前$i$位一样，这样我们又可以去掉枚举$m$个类的那个循环

#### 代码(超时)
```c++
/**
 *    author: andif
 *    created: 16.12.2023 15:37:36
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
const int B = 10;
const int mod = 2520;


vi id(mod + 1, -1), lm;
ll dp[20][mod + 1][50][2];

int gcd(int a, int b) { return b ? gcd(b, a % b) : a; }

int get_lcm(const vi& a) {
    int ret = 1;
    for (auto x: a) {
        ret = ret / gcd(ret, x) * x;
    }
    return ret;
}

vi get_all_lcm() {
    vi ret;
    set<int> s;
    rep(i, 1, (1 << B)) {
        vi tmp;
        rep(j, 1, B) {
            if ((i >> j) & 1) {
                tmp.eb(j);
            }
        }
        s.insert(get_lcm(tmp));
    }
    for (auto x: s) ret.push_back(x);
    // de(sz(ret));
    return ret;
}

ll gao(ll n) {
    vi c;
    for (; n; n /= 10) c.eb(n % 10);
    c.eb(0);
    reverse(all(c));
    int m = sz(c) - 1;

    ll ret = 0;
    mt(dp, 0);
    dp[0][0][id[1]][1] = 1;
    rep(i, 0, m) {
        rep(j, 0, mod) {
            rep(k, 0, sz(lm)) {
                rep(h, 0, 2) {
                    if (!dp[i][j][k][h]) continue;
                    int d = c[i + 1];
                    int up = h ? d : B;
                    rep(x, 0, up) {
                        int nj = (j * 10 + x) % mod;
                        int nk = x ? (lm[k] / gcd(lm[k], x) * x) : lm[k];
                        dp[i + 1][nj][id[nk]][0] += dp[i][j][k][h];
                    }
                    if (h) {
                        int nj = (j * 10 + d) % mod;
                        int nk = d ? (lm[k] / gcd(lm[k], d) * d) : lm[k];
                        dp[i + 1][nj][id[nk]][1] += dp[i][j][k][h];
                    }
                }
            }
        }
    }
    rep(j, 0, mod)
        rep(k, 0, sz(lm)) {
            if (j % lm[k]) continue;
            // if (dp[m][j][k][0] || dp[m][j][k][1])
            //     dd(j), dd(k), dd(dp[m][j][k][0]), de(dp[m][j][k][1]);
            ret += dp[m][j][k][0] + dp[m][j][k][1];
        }
    return ret;
}

int main() {
    qc;
    lm = get_all_lcm();
    rep(i, 0, sz(lm)) id[lm[i]] = i;
    int t; cin >> t;
    while(t--) {
        ll l, r; cin >> l >> r;
        // gao(r);
        cout << gao(r) - gao(l - 1) << '\n';
    }
    return 0;
}
```

### 第四次尝试

#### 思路

去掉$446$行的循环，改成两个$if$的形式，

这样为什么可以快呢？

我也不知道，希望有大佬可以教我下，谢谢！！

#### 代码(超时)

```c++
/**
 *    author: andif
 *    created: 16.12.2023 15:37:36
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
const int B = 10;
const int mod = 2520;


vi id(mod + 1, -1), lm;
ll dp[20][mod + 1][50][2];

int gcd(int a, int b) { return b ? gcd(b, a % b) : a; }

int get_lcm(const vi& a) {
    int ret = 1;
    for (auto x: a) {
        ret = ret / gcd(ret, x) * x;
    }
    return ret;
}

vi get_all_lcm() {
    vi ret;
    set<int> s;
    rep(i, 1, (1 << B)) {
        vi tmp;
        rep(j, 1, B) {
            if ((i >> j) & 1) {
                tmp.eb(j);
            }
        }
        s.insert(get_lcm(tmp));
    }
    for (auto x: s) ret.push_back(x);
    return ret;
}

ll gao(ll n) {
    vi c;
    for (; n; n /= 10) c.eb(n % 10);
    c.eb(0);
    reverse(all(c));
    int m = sz(c) - 1;
    ll ret = 0;
    mt(dp, 0);
    dp[0][0][id[1]][1] = 1;
    rep(i, 0, m) {
        rep(j, 0, mod) {
            rep(k, 0, sz(lm)) {
                if (dp[i][j][k][0]) {
                    rep(x, 0, B) {
                        int nj = (j * 10 + x) % mod;
                        int nk = x ? (lm[k] / gcd(lm[k], x) * x) : lm[k];
                        dp[i + 1][nj][id[nk]][0] += dp[i][j][k][0];
                    }
                }
                if (dp[i][j][k][1]) {
                    int d = c[i + 1];
                    rep(x, 0, d) {
                        int nj = (j * 10 + x) % mod;
                        int nk = x ? (lm[k] / gcd(lm[k], x) * x) : lm[k];
                        dp[i + 1][nj][id[nk]][0] += dp[i][j][k][1];
                    }
                    int nj = (j * 10 + d) % mod;
                    int nk = d ? (lm[k] / gcd(lm[k], d) * d) : lm[k];
                    dp[i + 1][nj][id[nk]][1] += dp[i][j][k][1];

                }
            }
        }
    }
    rep(j, 0, mod)
        rep(k, 0, sz(lm)) {
            if (j % lm[k]) continue;
            ret += dp[m][j][k][0] + dp[m][j][k][1];
        }
    return ret;
}

int main() {
    qc;
    lm = get_all_lcm();
    rep(i, 0, sz(lm)) id[lm[i]] = i;
    int t; cin >> t;
    while(t--) {
        ll l, r; cin >> l >> r;
        // gao(r);
        cout << gao(r) - gao(l - 1) << '\n';
    }
    return 0;
}
```

### 第五次尝试

#### 思路

$gcd$还有一些时间浪费了！！！

#### 代码(AC)

```c++
/**
 *    author: andif
 *    created: 16.12.2023 15:37:36
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
const int B = 10;
const int mod = 2520;


vi id(mod + 1, -1), lm;
ll dp[20][mod + 1][50][2];
int g[mod + 1][mod + 1];

int gcd(int a, int b) { return b ? gcd(b, a % b) : a; }

int get_lcm(const vi& a) {
    int ret = 1;
    for (auto x: a) {
        ret = ret / gcd(ret, x) * x;
    }
    return ret;
}

vi get_all_lcm() {
    vi ret;
    set<int> s;
    rep(i, 1, (1 << B)) {
        vi tmp;
        rep(j, 1, B) {
            if ((i >> j) & 1) {
                tmp.eb(j);
            }
        }
        s.insert(get_lcm(tmp));
    }
    for (auto x: s) ret.push_back(x);
    return ret;
}

ll gao(ll n) {
    vi c;
    for (; n; n /= 10) c.eb(n % 10);
    c.eb(0);
    reverse(all(c));
    int m = sz(c) - 1;
    ll ret = 0;
    mt(dp, 0);
    dp[0][0][id[1]][1] = 1;
    rep(i, 0, m) {
        rep(j, 0, mod) {
            rep(k, 0, sz(lm)) {
                if (dp[i][j][k][0]) {
                    rep(x, 0, B) {
                        int nj = (j * 10 + x) % mod;
                        int nk = x ? (lm[k] / g[lm[k]][x] * x) : lm[k];
                        dp[i + 1][nj][id[nk]][0] += dp[i][j][k][0];
                    }
                }
                if (dp[i][j][k][1]) {
                    int d = c[i + 1];
                    rep(x, 0, d) {
                        int nj = (j * 10 + x) % mod;
                        int nk = x ? (lm[k] / g[lm[k]][x] * x) : lm[k];
                        dp[i + 1][nj][id[nk]][0] += dp[i][j][k][1];
                    }
                    int nj = (j * 10 + d) % mod;
                    int nk = d ? (lm[k] / g[lm[k]][d] * d) : lm[k];
                    dp[i + 1][nj][id[nk]][1] += dp[i][j][k][1];

                }
            }
        }
    }
    rep(j, 0, mod)
        rep(k, 0, sz(lm)) {
            if (j % lm[k]) continue;
            ret += dp[m][j][k][0] + dp[m][j][k][1];
        }
    return ret;
}

int main() {
    qc;
    lm = get_all_lcm();
    rep(i, 0, sz(lm)) id[lm[i]] = i;
    rep(i, 0, mod + 1) rep(j, 0, mod + 1) g[i][j] = gcd(i, j);
    int t; cin >> t;
    while(t--) {
        ll l, r; cin >> l >> r;
        // gao(r);
        cout << gao(r) - gao(l - 1) << '\n';
    }
    return 0;
}
```


