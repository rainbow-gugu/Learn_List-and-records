# <span style="color:#1e3a8a;">题解：分组异或与按位与最大值（线性基）</span>

## <span style="color:#2563eb;">题意简述</span>

有 $n$ 个正整数，将它们分成若干非空组。每组的价值为组内所有数的异或和，总价值为所有组价值的**按位与**，求最大总价值。

## <span style="color:#2563eb;">核心 Trick</span>

### <span style="color:#3b82f6;">第一步：证明最多分 2 组</span>

若最优分组有至少 $3$ 组，且答案某一位为 $1$，则所有组的异或和在这一位都是 $1$。

任选三组合并成一组，新组异或和在这一位为：

$$\Large 1 \oplus 1 \oplus 1 = 1$$

其他组不变，所以这一位仍然为 $1$，答案不会变劣。反复合并直到组数 $\le 2$。

### <span style="color:#3b82f6;">第二步：最高位奇偶性判断</span>

设全局最高位为 $h$，统计第 $h$ 位为 $1$ 的数的个数：

- **奇数个 → 分 1 组**：分 2 组时 $x \oplus y = \text{sum}$，而 $\text{sum}$ 最高位为 $1$，故 $x$ 与 $y$ 最高位不同，$x \& y$ 最高位为 $0$，不如分 1 组（$\text{sum}$ 最高位为 $1$）
- **偶数个 → 分 2 组**：分 1 组时 $\text{sum}$ 最高位为 $0$；分 2 组可让两组各含奇数个最高位为 $1$ 的数，使 $x$ 与 $y$ 最高位均为 $1$，从而 $x \& y$ 最高位为 $1$

### <span style="color:#3b82f6;">第三步：偶数情况的线性基求解</span>

分 2 组时 $x \oplus y = \text{sum}$，因此 $x \& y$ 只能在 $\text{sum}$ 为 $0$ 的位上取 $1$（$\text{sum}$ 为 $1$ 的位上 $x$ 与 $y$ 不同，按位与必为 $0$）。

将每个数中 $\text{sum}$ 为 $1$ 的位清零：

$$\Large b_i = a_i \mathbin{\&} (\sim\text{sum})$$

清零后全部数的异或和为 $0$，故两组异或和相等 $x' = y'$，此时 $x \& y = x'$。最大化 $x \& y$ 等价于从 $b_i$ 中选子集使异或和最大，用**贪心线性基**求解。

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
const int MAXBIT = 60;
ll p[MAXBIT + 5], d[MAXBIT + 6];
int cnt;

void insert(ll x) {
    for (int i = MAXBIT; i >= 0; i--) {
        if (!(x >> i & 1)) {
            continue;
        }
        if (!p[i]) {
            p[i] = x;
            return;
        }
        x ^= p[i];
    }
}

ll max_xor() {
    ll ans = 0;
    for (int i = MAXBIT; i >= 0; i--) {
        if ((ans ^ p[i]) > ans) {
            ans ^= p[i];
        }
    }
    return ans;
}

vector<ll> a;

void solve() {
    int n;
    cin >> n;
    vector<ll> a(n);
    ll sum = 0;
    int highest = 0;
    for (int i = 0; i < n; i++) {
        cin >> a[i];
        sum ^= a[i];
        for (int b = 60; b >= 0; b--)
            if (a[i] >> b & 1) {
                highest = max(highest, b);
                break;
            }
    }
    int cnt = 0;
    for (int i = 0; i < n; i++)
        if (a[i] >> highest & 1) {
            cnt++;
        }
    if (cnt % 2 == 1) {
        cout << sum << endl;
        return;
    }
    memset(p, 0, sizeof p);
    for (int i = 0; i < n; i++) {
        insert(a[i] & (~sum));
    }
    cout << max_xor() << endl;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);
    ll t = 1;
    cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
