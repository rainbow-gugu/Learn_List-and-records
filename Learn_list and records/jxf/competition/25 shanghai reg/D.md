

# <span style="color:#1e3a8a;">题解：三进制状态 DP（查询异或和）</span>

## <span style="color:#2563eb;">题意简述</span>

给定长度为 $2^n$ 的数组 $a$。定义查询串 $q$ 是一个长度为 $n$ 的字符串，每位为 `0`、`1` 或 `?`。

$S(q)$ 表示所有满足 $q$ 限制的下标 $j$（`?` 位置可以是 0 或 1）对应的 $a[j]$ 之和。

求所有 $3^n$ 个查询串的 $S(q)$ 的**异或和**。

## <span style="color:#2563eb;">关键转化</span>

把 `?` 看成三进制的 $2$，每个查询串对应一个唯一的三进制数。这样所有查询串可以用一个整数索引表示，便于数组存储和状态转移。

## <span style="color:#2563eb;">核心递推</span>

如果 $q$ 的第 $k$ 位是 `?`，则：

$$\Large S(q) = S(q\text{ 第}k\text{位改}0) + S(q\text{ 第}k\text{位改}1)$$

因为 `?` 对应该位可以是 0 或 1，两部分加起来就是总和。

**递推顺序**：按 `?` 的最高位从小到大处理。

- **基础情况**：没有 `?` 的查询串（共 $2^n$ 个），$S(q) = a[q]$
- **递推**：第 $k$ 位为 `?` 的状态，由第 $k$ 位为 0 和 1 的两个状态相加得到；这两个状态的 `?` 最高位都 $< k$，已经计算完毕

## <span style="color:#2563eb;">复杂度分析</span>

$$\Large O(3^n)$$

以 $n = 16$ 为例：

- $3^{16} = 43046721$ 个状态
- `int` 数组约 $164\ \text{MB}$
- 时间约 $4300$ 万次操作

## <span style="color:#2563eb;">代码实现</span>

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef __int128 lll;
typedef pair<ll, ll> PLL;
typedef pair<int, int> PII;
typedef tuple<int, int, int> TII;
#define endl '\n'
const ll N = 2e5 + 5;
const ll INF = 1e18, MINF = -1e18;
const ll MAXN = 43046721;
vector<ll> pow3;
int f[MAXN];
vector<ll> hight[17];

void solve() {
    ll n;
    cin >> n;
    ll total = 1 << n;
    pow3.assign(17, 0);
    pow3[0] = 1;
    for (int i = 1; i < 17; i++) {
        pow3[i] = pow3[i - 1] * 3;
    }
    ll ans = 0;
    ll R = 0;
    for (int i = 0; i < total; i++) {
        ll x;
        cin >> x;
        ll idx = 0;
        for (int j = 0; j < 17; j++) {
            if (i & (1 << j)) {
                idx += pow3[j];
            }
        }
        f[idx] = x;
        R = max(R, idx);
        ans ^= x;
    }
    for (int i = 0; i < n; i++) {
        int hb = n - 1 - i;
        hight[i].resize(1 << hb);
        for (int j = 0; j < (1 << hb); j++) {
            ll idx = 0;
            for (int k = 0; k < hb; k++) {
                if (j & (1 << k)) {
                    idx += pow3[i + 1 + k];
                }
            }
            hight[i][j] = idx;
        }
    }
    for (int k = 0; k < n; k++) {
        ll pk = pow3[k];
        for (int j = 0; j < (1 << (n - k - 1)); j++) {
            ll ht = hight[k][j];
            ll b0 = ht;
            ll b1 = ht + pk;
            ll b2 = ht + 2 * pk;
            for (ll low = 0; low < pk; low++) {
                f[b2 + low] = f[b1 + low] + f[b0 + low];
                ans ^= f[b2 + low];
            }
        }
    }
    cout << ans << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);
    ll t = 1;
    // cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
