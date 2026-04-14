# LeetCode 121: Best Time to Buy and Sell Stock (k = 1)

## The General Framework (Quick Recap)

We define:

```
T[i][k][0] = max profit at end of day i, at most k transactions, holding 0 stock
T[i][k][1] = max profit at end of day i, at most k transactions, holding 1 stock
```

General recurrence:

```
T[i][k][0] = max(T[i-1][k][0],   T[i-1][k][1] + prices[i])
                  ^^^^^^^^^^^      ^^^^^^^^^^^^^^^^^^^^^^^^^
                  do nothing        sell today

T[i][k][1] = max(T[i-1][k][1],   T[i-1][k-1][0] - prices[i])
                  ^^^^^^^^^^^      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                  do nothing        buy today (uses 1 transaction)
```

---

## Applying k = 1

Plug `k = 1` into the second equation:

```
T[i][1][1] = max(T[i-1][1][1], T[i-1][0][0] - prices[i])
```

### ❓ Why is `T[i-1][0][0]` always 0?

Let's break down what `T[i][0][0]` means:

| Part | Meaning |
|------|---------|
| `i` | Day i |
| `0` (k) | **Zero** transactions allowed |
| `0` (stock) | Holding **no** stock |

> If you're allowed **zero transactions**, you can **never buy or sell**.
> You just watch the market do its thing.
> Your profit is **always 0**, regardless of the day.

```
T[i][0][0] = 0    for ALL values of i
```

This is a **base case** of the framework, not something derived — it's true by definition.

### Simplified Equations

Substituting `T[i-1][0][0] = 0`:

```
T[i][1][0] = max(T[i-1][1][0], T[i-1][1][1] + prices[i])   // rest or sell
T[i][1][1] = max(T[i-1][1][1], -prices[i])                   // rest or buy
```

### What does `-prices[i]` mean?

It means: "if I buy today, my profit is `0 - prices[i]`" (I spent money, so profit is negative).

Over time, `T[i][1][1]` becomes the **negative of the minimum price seen so far**.

```
Day 0: price=7  →  T_i11 = max(-INF, -7) = -7     (bought at 7)
Day 1: price=1  →  T_i11 = max(-7, -1)   = -1     (bought at 1, cheaper!)
Day 2: price=5  →  T_i11 = max(-1, -5)   = -1     (keep day 1's buy)
```

`T_i11 = -1` means "the best buying decision so far costs 1 dollar."

---

## Visual Walkthrough

```
prices = [7, 1, 5, 3, 6, 4]

Day  Price  T_i10 (no stock)         T_i11 (holding)           
---  -----  ----------------------   ----------------------
 0     7    max(0, -INF+7) = 0       max(-INF, -7)  = -7       
 1     1    max(0, -7+1)   = 0       max(-7, -1)    = -1  ← found cheaper buy
 2     5    max(0, -1+5)   = 4  ←    max(-1, -5)    = -1       
 3     3    max(4, -1+3)   = 4       max(-1, -3)    = -1       
 4     6    max(4, -1+6)   = 5  ←    max(-1, -6)    = -1       
 5     4    max(5, -1+4)   = 5       max(-1, -4)    = -1       

Answer: 5 (buy at price 1, sell at price 6)
```

---

## The Code (C++)

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

int maxProfit(vector<int>& prices) {
    int T_i10 = 0;          // best profit, not holding stock
    int T_i11 = INT_MIN;    // best profit, holding stock

    for (int price : prices) {
        T_i10 = max(T_i10, T_i11 + price);  // rest or sell
        T_i11 = max(T_i11, -price);          // rest or buy
    }

    return T_i10;
}

int main() {
    vector<int> prices = {7, 1, 5, 3, 6, 4};
    cout << "Max Profit: " << maxProfit(prices) << endl;  // Output: 5
    return 0;
}
```

### Complexity

| | Value |
|---|---|
| Time | O(n) — single pass |
| Space | O(1) — two variables |

---

## Why This Works (Intuition)

The two variables are doing this behind the scenes:

- `T_i11` = "what's the cheapest I could have bought?" → tracks **min price**
- `T_i10` = "what's the best I could sell for?" → tracks **max(price - min_price_so_far)**

This is equivalent to the classic one-pass solution:

```cpp
int maxProfit(vector<int>& prices) {
    int minPrice = INT_MAX, maxProfit = 0;
    for (int p : prices) {
        minPrice = min(minPrice, p);
        maxProfit = max(maxProfit, p - minPrice);
    }
    return maxProfit;
}
```

Both do the exact same thing — the DP framework just expresses it in a way that generalizes to all 6 problems.
