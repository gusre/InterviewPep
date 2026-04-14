# The Stock Problem Series — One Framework, Six Solutions

A unified DP approach to solve all 6 LeetCode stock problems.

| # | Problem | Constraint |
|---|---------|-----------|
| 121 | Best Time to Buy and Sell Stock | k = 1 |
| 122 | Best Time to Buy and Sell Stock II | k = ∞ |
| 123 | Best Time to Buy and Sell Stock III | k = 2 |
| 188 | Best Time to Buy and Sell Stock IV | k = any |
| 309 | Best Time to Buy and Sell Stock with Cooldown | k = ∞, 1-day cooldown |
| 714 | Best Time to Buy and Sell Stock with Transaction Fee | k = ∞, fee per txn |

---

## Part I — The General Framework

### The 3 Factors

Every stock problem is defined by 3 things:

| Factor | Symbol | Meaning |
|--------|--------|---------|
| Day | `i` | Which day (0 to n-1) |
| Transactions | `k` | Max buy-sell pairs allowed |
| Holdings | `0` or `1` | Are we holding a stock right now? |

### State Definition

```
T[i][k][0] = max profit at end of day i, at most k transactions, holding 0 stock
T[i][k][1] = max profit at end of day i, at most k transactions, holding 1 stock
```

### Base Cases

```
T[-1][k][0] =  0          Before any day, no stock → profit is 0
T[-1][k][1] = -Infinity    Before any day, holding stock → impossible
T[i][0][0]  =  0          Zero transactions allowed, no stock → profit is 0
T[i][0][1]  = -Infinity    Zero transactions allowed, holding → impossible
```

`-Infinity` means "this state can never happen."

### Recurrence Relations

On each day, 3 choices: **buy**, **sell**, or **rest**.

**Ending the day with 0 stock** (either rested or sold):

```
T[i][k][0] = max( T[i-1][k][0],  T[i-1][k][1] + prices[i] )
                   ^^^^^^^^^^^^    ^^^^^^^^^^^^^^^^^^^^^^^^^^
                   rest             sell today
```

**Ending the day with 1 stock** (either rested or bought):

```
T[i][k][1] = max( T[i-1][k][1],  T[i-1][k-1][0] - prices[i] )
                   ^^^^^^^^^^^^    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                   rest             buy today (starts new txn, so k-1)
```

> 💡 Only **buy** decreases `k` because a transaction = buy + sell pair, and we count it at the buy.

**Final answer** is always `T[n-1][k][0]` — last day, not holding (holding would mean unrealized loss).

---

## Part II — The Six Cases

---

### Case I: k = 1 (LeetCode 121)

**Problem:** At most 1 transaction.

**Recurrence with k=1:**

```
T[i][1][0] = max(T[i-1][1][0], T[i-1][1][1] + prices[i])
T[i][1][1] = max(T[i-1][1][1], T[i-1][0][0] - prices[i])
```

**Why `T[i-1][0][0]` is always 0:**
`T[i][0][0]` = "max profit with 0 transactions allowed, holding nothing."
Zero transactions means you can never buy or sell. Profit = 0. Always. Every day.

**Simplified:**

```
T[i][1][0] = max(T[i-1][1][0], T[i-1][1][1] + prices[i])   // rest or sell
T[i][1][1] = max(T[i-1][1][1], -prices[i])                   // rest or buy
```

`T_i11` tracks the negative of the minimum price seen so far.
`T_i10` tracks the maximum of (price - min_price_so_far).

**Walkthrough:** `prices = [7, 1, 5, 3, 6, 4]`

```
Day  Price  T_i10 (no stock)         T_i11 (holding)
---  -----  ----------------------   ----------------------
 0     7    max(0, -INF+7) = 0       max(-INF, -7)  = -7
 1     1    max(0, -7+1)   = 0       max(-7, -1)    = -1  ← cheaper buy
 2     5    max(0, -1+5)   = 4       max(-1, -5)    = -1
 3     3    max(4, -1+3)   = 4       max(-1, -3)    = -1
 4     6    max(4, -1+6)   = 5 ←     max(-1, -6)    = -1
 5     4    max(5, -1+4)   = 5       max(-1, -4)    = -1

Answer: 5 (buy@1, sell@6)
```

**C++ — O(n) time, O(1) space:**

```cpp
int maxProfit(vector<int>& prices) {
    int T_i10 = 0, T_i11 = INT_MIN;
    for (int p : prices) {
        T_i10 = max(T_i10, T_i11 + p);
        T_i11 = max(T_i11, -p);
    }
    return T_i10;
}
```

> This is equivalent to the classic "track min price, compute max diff" approach.

---

### Case II: k = +Infinity (LeetCode 122)

**Problem:** Unlimited transactions.

**Key insight:** When k is infinite, `k` and `k-1` are the same — the limit never matters.
So `T[i-1][k-1][0]` = `T[i-1][k][0]`, and `k` drops out of the equations.

**Recurrence:**

```
T[i][k][0] = max(T[i-1][k][0], T[i-1][k][1] + prices[i])   // rest or sell
T[i][k][1] = max(T[i-1][k][1], T[i-1][k][0] - prices[i])   // rest or buy
```

**Walkthrough:** `prices = [1, 3, 2, 8]`

```
Day  Price  T_ik0 (no stock)          T_ik1 (holding)
---  -----  -----------------------   -----------------------
 0     1    max(0, -INF+1)  = 0       max(-INF, 0-1)  = -1
 1     3    max(0, -1+3)    = 2       max(-1, 0-3)    = -1
 2     2    max(2, -1+2)    = 2       max(-1, 2-2)    = 0
 3     8    max(2, 0+8)     = 8       max(0, 2-8)     = 0

Answer: 8 (buy@1 sell@3, buy@2 sell@8 → profit 2+6=8)
```

**C++ — O(n) time, O(1) space:**

```cpp
int maxProfit(vector<int>& prices) {
    int T_ik0 = 0, T_ik1 = INT_MIN;
    for (int p : prices) {
        int T_ik0_old = T_ik0;
        T_ik0 = max(T_ik0, T_ik1 + p);
        T_ik1 = max(T_ik1, T_ik0_old - p);
    }
    return T_ik0;
}
```

> We save `T_ik0_old` because both equations use the previous day's value.
> Note: caching `T_ik0_old` is technically unnecessary here — the greedy approach
> (accumulate every positive difference) gives the same result.

---

### Case III: k = 2 (LeetCode 123)

**Problem:** At most 2 transactions.

**4 variables per day:** `T[i][1][0]`, `T[i][1][1]`, `T[i][2][0]`, `T[i][2][1]`

**Recurrence:**

```
T[i][2][0] = max(T[i-1][2][0], T[i-1][2][1] + prices[i])   // rest or sell (2nd txn)
T[i][2][1] = max(T[i-1][2][1], T[i-1][1][0] - prices[i])   // rest or buy  (2nd txn)
T[i][1][0] = max(T[i-1][1][0], T[i-1][1][1] + prices[i])   // rest or sell (1st txn)
T[i][1][1] = max(T[i-1][1][1], -prices[i])                   // rest or buy  (1st txn)
```

**Walkthrough:** `prices = [3, 3, 5, 0, 0, 3, 1, 4]`

```
Day  Price  T_i20  T_i21  T_i10  T_i11
---  -----  -----  -----  -----  -----
 0     3      0     -3      0     -3
 1     3      0     -3      0     -3
 2     5      2     -3      2     -3
 3     0      2      2      0      0
 4     0      2      2      0      0
 5     3      5      2      3      0
 6     1      5      2      3      0
 7     4      6      2      4      0

Answer: 6 (buy@0 sell@5 → +3, buy@1(price 0) sell@4(price 4) → +4... 
         actually: buy@3 sell@5 → +3, buy@6 sell@7 → +3 = 6)
```

**C++ — O(n) time, O(1) space:**

```cpp
int maxProfit(vector<int>& prices) {
    int T_i10 = 0, T_i11 = INT_MIN;
    int T_i20 = 0, T_i21 = INT_MIN;
    for (int p : prices) {
        T_i20 = max(T_i20, T_i21 + p);
        T_i21 = max(T_i21, T_i10 - p);
        T_i10 = max(T_i10, T_i11 + p);
        T_i11 = max(T_i11, -p);
    }
    return T_i20;
}
```

---

### Case IV: k = arbitrary (LeetCode 188)

**Problem:** At most `k` transactions.

**Key optimization:** A transaction needs at least 2 days (buy + sell on different days).
So max useful transactions = `n/2`. If `k >= n/2`, treat it as k = ∞ (Case II).

**For general k:** maintain arrays of size `k+1` for both states.

**Recurrence (for each j from k down to 1):**

```
T_ik0[j] = max(T_ik0[j], T_ik1[j] + price)
T_ik1[j] = max(T_ik1[j], T_ik0[j-1] - price)
```

> Loops backward to avoid using already-updated values (same trick as 0/1 knapsack).

**C++ — O(kn) time, O(k) space:**

```cpp
int maxProfit(int k, vector<int>& prices) {
    int n = prices.size();

    // Optimization: if k >= n/2, same as unlimited (Case II)
    if (k >= n / 2) {
        int T_ik0 = 0, T_ik1 = INT_MIN;
        for (int p : prices) {
            int old = T_ik0;
            T_ik0 = max(T_ik0, T_ik1 + p);
            T_ik1 = max(T_ik1, old - p);
        }
        return T_ik0;
    }

    // General case
    vector<int> T_ik0(k + 1, 0);
    vector<int> T_ik1(k + 1, INT_MIN);

    for (int p : prices) {
        for (int j = k; j > 0; j--) {
            T_ik0[j] = max(T_ik0[j], T_ik1[j] + p);
            T_ik1[j] = max(T_ik1[j], T_ik0[j - 1] - p);
        }
    }
    return T_ik0[k];
}
```

---

### Case V: k = +Infinity with Cooldown (LeetCode 309)

**Problem:** Unlimited transactions, but after selling you must wait 1 day before buying again.

**How cooldown changes things:**

In Case II (no cooldown), when buying on day `i`, we used yesterday's no-stock state:

```
T[i][k][1] = max(T[i-1][k][1], T[i-1][k][0] - prices[i])
                                 ^^^^^^^ yesterday
```

With cooldown, if we sell on day `i-1`, we can't buy on day `i`. So when buying, we must use
the state from **2 days ago**:

```
T[i][k][1] = max(T[i-1][k][1], T[i-2][k][0] - prices[i])
                                 ^^^^^^^ two days ago!
```

The sell equation stays the same:

```
T[i][k][0] = max(T[i-1][k][0], T[i-1][k][1] + prices[i])
```

**Walkthrough:** `prices = [1, 2, 3, 0, 2]`

```
Day  Price  T_ik0 (no stock)         T_ik1 (holding)          T_ik0_pre (2 days ago)
---  -----  ----------------------   ----------------------   ----------------------
 0     1    max(0, -INF+1)  = 0      max(-INF, 0-1)  = -1    0
 1     2    max(0, -1+2)    = 1      max(-1, 0-2)    = -1    0
 2     3    max(1, -1+3)    = 2      max(-1, 0-3)    = -1    1
 3     0    max(2, -1+0)    = 2      max(-1, 1-0)    = 1     2
 4     2    max(2, 1+2)     = 3      max(1, 2-2)     = 1     2

Answer: 3 (buy@0 sell@2 → +2, cooldown day3, buy@3 sell@4 → +2... 
         actually: buy@0 sell@1 → +1, cool, buy@3 sell@4 → +2 = 3)
```

**C++ — O(n) time, O(1) space:**

```cpp
int maxProfit(vector<int>& prices) {
    int T_ik0 = 0, T_ik0_pre = 0, T_ik1 = INT_MIN;
    for (int p : prices) {
        int T_ik0_old = T_ik0;
        T_ik0 = max(T_ik0, T_ik1 + p);
        T_ik1 = max(T_ik1, T_ik0_pre - p);   // use 2-days-ago state
        T_ik0_pre = T_ik0_old;                // save for next iteration
    }
    return T_ik0;
}
```

> `T_ik0_pre` always holds the no-stock profit from **2 days ago** — that's the cooldown.

---

### Case VI: k = +Infinity with Transaction Fee (LeetCode 714) ⭐

**Problem:** Unlimited transactions, but each transaction costs a `fee`.

**How fee changes things:**

Same as Case II, but we subtract `fee` once per transaction. Since a transaction = buy + sell,
we can subtract the fee at either point. Two options:

**Option A — subtract fee when selling:**

```
T[i][k][0] = max(T[i-1][k][0], T[i-1][k][1] + prices[i] - fee)
T[i][k][1] = max(T[i-1][k][1], T[i-1][k][0] - prices[i])
```

**Option B — subtract fee when buying:**

```
T[i][k][0] = max(T[i-1][k][0], T[i-1][k][1] + prices[i])
T[i][k][1] = max(T[i-1][k][1], T[i-1][k][0] - prices[i] - fee)
```

Both are correct. The fee is paid exactly once per buy-sell pair either way.

**Walkthrough (Option A):** `prices = [1, 3, 2, 8, 4, 9], fee = 2`

```
Day  Price  T_ik0 (no stock)              T_ik1 (holding)
---  -----  ----------------------------  ----------------------------
 0     1    max(0, -INF+1-2)    = 0       max(-INF, 0-1)    = -1
 1     3    max(0, -1+3-2)      = 0       max(-1, 0-3)      = -1
 2     2    max(0, -1+2-2)      = 0       max(-1, 0-2)      = -1
 3     8    max(0, -1+8-2)      = 5       max(-1, 0-8)      = -1
 4     4    max(5, -1+4-2)      = 5       max(-1, 5-4)      = 1
 5     9    max(5, 1+9-2)       = 8       max(1, 5-9)       = 1

Answer: 8 (buy@0 sell@3 → 8-1-2=5, buy@4 sell@5 → 9-4-2=3, total=8)
```

**C++ Solution A (fee on sell) — O(n) time, O(1) space:**

```cpp
int maxProfit(vector<int>& prices, int fee) {
    long T_ik0 = 0, T_ik1 = INT_MIN;
    for (int p : prices) {
        long old = T_ik0;
        T_ik0 = max(T_ik0, T_ik1 + p - fee);
        T_ik1 = max(T_ik1, old - p);
    }
    return (int)T_ik0;
}
```

> ⚠️ Uses `long` to prevent overflow: `T_ik1 + p - fee` can underflow with `INT_MIN`.

**C++ Solution B (fee on buy) — O(n) time, O(1) space:**

```cpp
int maxProfit(vector<int>& prices, int fee) {
    int T_ik0 = 0, T_ik1 = INT_MIN;
    for (int p : prices) {
        int old = T_ik0;
        T_ik0 = max(T_ik0, T_ik1 + p);
        T_ik1 = max(T_ik1, old - p - fee);
    }
    return T_ik0;
}
```

> No overflow risk here since we subtract fee from `old - p` which is a reasonable range.

---

## Part III — Summary

### The Master Recurrence

```
T[i][k][0] = max(T[i-1][k][0], T[i-1][k][1] + prices[i])
T[i][k][1] = max(T[i-1][k][1], T[i-1][k-1][0] - prices[i])
```

### How Each Problem Modifies It

| Problem | k | Modification | Variables |
|---------|---|-------------|-----------|
| 121 | 1 | `T[i-1][0][0] = 0` → simplifies buy to `-prices[i]` | 2 |
| 122 | ∞ | `k-1 = k` → k drops out | 2 |
| 123 | 2 | Expand for k=1 and k=2 | 4 |
| 188 | any | Arrays of size k; optimize when `k >= n/2` | 2k |
| 309 | ∞ | Cooldown: use `T[i-2]` instead of `T[i-1]` when buying | 3 |
| 714 | ∞ | Fee: subtract `fee` on buy or sell | 2 |

### Complexity

| Problem | Time | Space |
|---------|------|-------|
| 121 | O(n) | O(1) |
| 122 | O(n) | O(1) |
| 123 | O(n) | O(1) |
| 188 | O(kn) | O(k) |
| 309 | O(n) | O(1) |
| 714 | O(n) | O(1) |

### The Pattern

```
         k=1          k=2          k=any
          │             │             │
          ▼             ▼             ▼
       2 vars       4 vars       2k vars
       (121)        (123)         (188)
                                    │
                              k >= n/2?
                              ┌─yes──┘
                              ▼
                           k = ∞
                           (122)
                          ╱      ╲
                   + cooldown    + fee
                      (309)      (714)
```

One framework. Six problems. All solved. 🎯
