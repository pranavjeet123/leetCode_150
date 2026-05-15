# 55 - Jump Game

**Difficulty:** Medium  
**LeetCode Link:** [#55](https://leetcode.com/problems/jump-game/)  
**Category:** Array / String  
**Status:** ✅ Solved

---

## Problem Statement

> You are given an integer array `nums`. You are initially positioned at index `0`, and each element represents your **maximum jump length** from that position. Return `true` if you can reach the last index, `false` otherwise.

**Example 1:**
```
Input:  nums = [2,3,1,1,4]
Output: true
Explanation: 0 →(+1)→ 1 →(+3)→ 4 (last index)
```

**Example 2:**
```
Input:  nums = [3,2,1,0,4]
Output: false
Explanation: Every path leads to index 3, where nums[3]=0 — stuck.
```

**Constraints:**
- `1 <= nums.length <= 10^4`
- `0 <= nums[i] <= 10^5`

---

## Approach

**Strategy:** Greedy — Track Maximum Reachable Index

**Intuition:**  
At any point, what matters is not *which* path you took, but *how far you can currently reach*. Maintain a single variable `max_reach` — the furthest index reachable from any index visited so far. For each index `i`:
- If `i > max_reach`, you can't stand here — it's an unreachable island → return `false`.
- Otherwise, extend `max_reach = max(max_reach, i + nums[i])`.

If the loop completes without hitting an unreachable index, you can reach the end → return `true`.

**Dry run — Example 1:** `[2, 3, 1, 1, 4]`
```
max_reach = 0

i=0: 0 <= 0 ✓  max_reach = max(0, 0+2) = 2
i=1: 1 <= 2 ✓  max_reach = max(2, 1+3) = 4
i=2: 2 <= 4 ✓  max_reach = max(4, 2+1) = 4
i=3: 3 <= 4 ✓  max_reach = max(4, 3+1) = 4
i=4: 4 <= 4 ✓  max_reach = max(4, 4+4) = 8
Loop ends → return true ✓
```

**Dry run — Example 2:** `[3, 2, 1, 0, 4]`
```
max_reach = 0

i=0: 0 <= 0 ✓  max_reach = max(0, 0+3) = 3
i=1: 1 <= 3 ✓  max_reach = max(3, 1+2) = 3
i=2: 2 <= 3 ✓  max_reach = max(3, 2+1) = 3
i=3: 3 <= 3 ✓  max_reach = max(3, 3+0) = 3
i=4: 4 > 3  ✗  → return false ✓
```

**Time Complexity:** O(n) — single pass  
**Space Complexity:** O(1)

---

## Solution

```python
from typing import List

class Solution:
    def canJump(self, nums: List[int]) -> bool:
        max_reach = 0
        for i in range(len(nums)):
            if i > max_reach:
                return False
            max_reach = max(max_reach, i + nums[i])
        return True
```

```javascript
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var canJump = function(nums) {
    let maxReach = 0;
    for (let i = 0; i < nums.length; i++) {
        if (i > maxReach) return false;
        maxReach = Math.max(maxReach, i + nums[i]);
    }
    return true;
};
```

---

## Edge Cases

- [x] Single element → `[0]` → already at last index → `true`
- [x] First element is 0 and length > 1 → `[0,1]` → `i=1 > maxReach=0` → `false`
- [x] All zeros except first → `[1,0,0]` → can reach index 1 but stuck → `false`
- [x] Large jumps → `[100,0,0,0]` → `maxReach=100` covers everything → `true`
- [x] Array of all zeros → `[0]` → length 1 → `true`; `[0,0]` → `false`

---

## Notes & Key Takeaways

- You never need to simulate actual jumps. The reachability of index `i` is fully captured by `max_reach`.
- `i + nums[i]` is the furthest you can reach *from* index `i` — nothing more complex than that.
- This greedy works because a larger `max_reach` is strictly better — there's never a reason to prefer a shorter reach.

---

## Related Problems

| # | Problem | Difference |
|---|---------|------------|
| 45 | Jump Game II | Find the **minimum** number of jumps to reach the end |
| 1306 | Jump Game III | Jump by exactly ±nums[i], can jump backwards |
| 1345 | Jump Game IV | Jump to same-value indices, BFS shortest path |
