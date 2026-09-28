
# <span style="color:#1e3a8a;">题解：博弈对称策略（异或游戏）</span>

## <span style="color:#2563eb;">题意简述</span>

有 $2n$ 个非负整数，Menji 和 Bot 轮流操作，Menji 先手。每回合：

- **Menji**：选一个数异或到累积值 $S$ 中，并删除该数
- **Bot**：选一个数直接删除

最终若 $S = 0$ 则 Menji 胜，否则 Bot 胜。

## <span style="color:#2563eb;">核心推断</span>

运用**博弈对称原理**：对于出现偶数次的数，Bot 采用配对策略——Menji 拿什么数，Bot 就拿同一个数（如果还有剩余）。这样偶数次的数必然被均分，每对一人一个。

配对部分对 $S$ 的异或贡献固定为：

$$\Large X = \bigoplus \left\{ x : \left\lfloor \frac{c_x}{2} \right\rfloor \text{ 为奇数} \right\}$$

出现奇数次的数配对后剩一个未配对，计入数组 $a$。未配对数的个数必为偶数（总数 $2n$ 为偶数，配对部分为偶数个）。

## <span style="color:#2563eb;">分情况讨论</span>

### <span style="color:#3b82f6;">情况一：未配对数为 0 个</span>

全部完全配对，Menji 胜当且仅当 $X = 0$。

### <span style="color:#3b82f6;">情况二：未配对数为 2 个</span>

设未配对数为 $u, v$。Menji 拿 $u$ 则 $S = X \oplus u$，要 $S = 0$ 需 $u = X$；Menji 拿 $v$ 则需 $v = X$。

故 Menji 胜当且仅当 $u = X$ 或 $v = X$。

### <span style="color:#3b82f6;">情况三：未配对数大于 2 个</span>

Bot 总能删掉 Menji 需要的搭档 $X \oplus a$，破坏 Menji 使 $S = 0$ 的尝试，Menji 无法获胜。

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

void solve() {
    ll n;
    cin >> n;
    map<ll, ll> ma;
    vector<ll> a;
    for (int i = 1; i <= 2 * n; i++) {
        ll x;
        cin >> x;
        ma[x]++;
    }
    ll sum = 0;
    for (auto it : ma) {
        if ((it.second / 2) % 2 == 1) {
            sum ^= it.first;
        }
        if (it.second % 2 == 1) {
            a.push_back(it.first);
        }
    }
    if (a.size() == 0) {
        if (sum == 0) {
            cout << "Menji" << endl;
        } else {
            cout << "Bot" << endl;
        }
    } else if (a.size() == 2) {
        if (a[0] == sum || a[1] == sum) {
            cout << "Menji" << endl;
        } else {
            cout << "Bot" << endl;
        }
    } else {
        cout << "Bot" << endl;
    }
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
