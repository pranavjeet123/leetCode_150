# 122 - Best Time to Buy and Sell Stock II

**Difficulty:** Medium  
**LeetCode Link:** [#122](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)  
**Category:** Array / String  
**Status:** ✅ Solved

---

## Problem Statement

> You are given an integer array `prices` where `prices[i]` is the price of a stock on the `i`th day. On each day you may buy and/or sell the stock. You can hold **at most one share** at a time, but you may sell and re-buy on the same day. Return the **maximum profit** you can achieve.

**Example 1:**
```
Input:  prices = [7,1,5,3,6,4]
Output: 7
Explanation: Buy day 2 (1), sell day 3 (5) → +4
             Buy day 4 (3), sell day 5 (6) → +3
             Total = 7
```

**Example 2:**
```
Input:  prices = [1,2,3,4,5]
Output: 4
Explanation: Buy day 1 (1), sell day 5 (5) → +4
             (or equivalently, capture every daily gain: 1+1+1+1 = 4)
```

**Example 3:**
```
Input:  prices = [7,6,4,3,1]
Output: 0
Explanation: Prices only fall — never buy.
```

**Constraints:**
- `1 <= prices.length <= 3 * 10^4`
- `0 <= prices[i] <= 10^4`

---

## Approach

**Strategy:** Greedy — Capture Every Upward Slope

**Intuition:**  
Because you can sell and re-buy on the same day, holding through a multi-day rise is identical to collecting each daily gain individually.

```
prices = [1, 2, 3, 4, 5]

Hold strategy:  buy at 1, sell at 5  → profit = 4
Daily strategy: (2-1) + (3-2) + (4-3) + (5-4) = 1+1+1+1 = 4  ← same result
```

So the maximum profit equals the **sum of all positive day-over-day differences**. Any negative difference (price dropped) means don't buy — just skip it by clamping to 0.

**Steps:**
1. Initialise `profit = 0`.
2. For each consecutive pair `(prices[i-1], prices[i])`:
   - Add `max(0, prices[i] - prices[i-1])` to `profit`.
3. Return `profit`.

**Dry run — Example 1:** `[7, 1, 5, 3, 6, 4]`
```
i=1: max(0, 1-7) = max(0,-6) = 0   → profit=0
i=2: max(0, 5-1) = max(0, 4) = 4   → profit=4
i=3: max(0, 3-5) = max(0,-2) = 0   → profit=4
i=4: max(0, 6-3) = max(0, 3) = 3   → profit=7
i=5: max(0, 4-6) = max(0,-2) = 0   → profit=7
Return 7 ✓
```

**Time Complexity:** O(n) — single pass  
**Space Complexity:** O(1)

---

## Solution

```python
from typing import List

class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        profit = 0
        for i in range(1, len(prices)):
            profit += max(0, prices[i] - prices[i - 1])
        return profit
```

```javascript
/**
 * @param {number[]} prices
 * @return {number}
 */
var maxProfit = function(prices) {
    let profit = 0;
    for (let i = 1; i < prices.length; i++) {
        profit += Math.max(0, prices[i] - prices[i - 1]);
    }
    return profit;
};
```

---

## Why Greedy Works Here (vs Problem #121)

| | #121 — Buy & Sell Once | #122 — Buy & Sell Many Times |
|---|---|---|
| Transactions | 1 | Unlimited |
| Strategy | Track global min, single best sell | Collect every upward slope |
| Greedy valid? | Yes — one optimal window | Yes — no benefit to skipping a gain |
| DP needed? | No | No |

In #121 you must find the single best buy→sell window. In #122, since you can always re-enter the market, every upward movement is free profit — skipping any positive slope can only hurt you.

---

## Edge Cases

- [x] Monotonically decreasing → `[7,6,4,3,1]` → all diffs negative → profit = 0
- [x] Monotonically increasing → `[1,2,3,4,5]` → sum all diffs = 4
- [x] Single day → `[5]` → loop never runs → profit = 0
- [x] All same prices → `[3,3,3]` → all diffs = 0 → profit = 0
- [x] Price = 0 → valid, constraint allows it

---

## Notes & Key Takeaways

- The key unlock: **sell and re-buy on the same day** makes every up-tick independently collectible.
- `max(0, diff)` is the entire decision logic — no state machine, no DP needed.
- This greedy fails for problems with transaction limits (#123 — at most 2 transactions) or cooldown periods (#309) — those need DP.

---

## Related Problems

| # | Problem | Difference |
|---|---------|------------|
| 121 | Best Time to Buy and Sell Stock | At most 1 transaction |
| 123 | Best Time to Buy and Sell Stock III | At most 2 transactions → DP |
| 188 | Best Time to Buy and Sell Stock IV | At most k transactions → DP |
| 309 | Best Time to Buy and Sell Stock with Cooldown | 1-day cooldown → DP |
