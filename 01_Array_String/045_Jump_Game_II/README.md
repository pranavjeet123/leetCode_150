# 45 - Jump Game II

**Difficulty:** Medium  
**LeetCode Link:** [#45](https://leetcode.com/problems/jump-game-ii/)  
**Category:** Array / String  
**Status:** ✅ Solved

---

## Problem Statement

> You are given a 0-indexed array `nums` of length `n`. Starting at index `0`, each element `nums[i]` represents the maximum forward jump length from index `i`. Return the **minimum number of jumps** to reach index `n - 1`. It is guaranteed you can always reach the last index.

**Example 1:**
```
Input:  nums = [2,3,1,1,4]
Output: 2
Explanation: Index 0 →(+1)→ 1 →(+3)→ 4  (2 jumps)
```

**Example 2:**
```
Input:  nums = [2,3,0,1,4]
Output: 2
```

**Constraints:**
- `1 <= nums.length <= 10^4`
- `0 <= nums[i] <= 1000`
- Always guaranteed to reach `n - 1`

---

## Approach

**Strategy:** Greedy — BFS by Layers

**Intuition:**  
Think of jumps as BFS levels. All indices reachable in exactly `k` jumps form one "level". From every index in the current level, scan ahead to find the furthest index reachable — that defines the next level. Count how many level transitions are needed to include index `n-1`.

Three variables:
- `jumps` — number of jumps taken so far
- `current_end` — the rightmost index reachable with `jumps` jumps (end of the current BFS level)
- `farthest` — the furthest index reachable from any index in the current level

When the scan reaches `current_end`, the current level is exhausted. Take one jump: `jumps++`, advance `current_end = farthest`.

We iterate only to `n - 2` — if we're already within `current_end` of the last index, the jump is already counted when we set that `current_end`.

**Dry run — Example 1:** `[2, 3, 1, 1, 4]`, n=5
```
jumps=0, current_end=0, farthest=0

i=0: farthest = max(0, 0+2) = 2
     i == current_end → JUMP: jumps=1, current_end=2

i=1: farthest = max(2, 1+3) = 4

i=2: farthest = max(4, 2+1) = 4
     i == current_end → JUMP: jumps=2, current_end=4

i=3: farthest = max(4, 3+1) = 4
     (loop ends at n-2=3)

Return 2 ✓
```

**Dry run — Example 2:** `[2, 3, 0, 1, 4]`, n=5
```
jumps=0, current_end=0, farthest=0

i=0: farthest = max(0, 0+2) = 2
     i == current_end → JUMP: jumps=1, current_end=2

i=1: farthest = max(2, 1+3) = 4

i=2: farthest = max(4, 2+0) = 4
     i == current_end → JUMP: jumps=2, current_end=4

i=3: farthest = max(4, 3+1) = 4
     (loop ends at n-2=3)

Return 2 ✓
```

**Visualised as BFS layers:**
```
nums = [2, 3, 1, 1, 4]
        ↑
        Start (index 0)

Jump 0 (start):  [0]              reachable: indices 0..0
Jump 1:          [1, 2]           reachable: indices 0..2  (max reach from 0 = 2)
Jump 2:          [3, 4]           reachable: indices 3..4  (max reach from 1,2 = 4)
                          ↑
                          index 4 = last index → 2 jumps
```

**Time Complexity:** O(n) — single pass  
**Space Complexity:** O(1)

---

## Solution

```python
from typing import List

class Solution:
    def jump(self, nums: List[int]) -> int:
        jumps = 0
        current_end = 0
        farthest = 0

        for i in range(len(nums) - 1):   # stop at n-2
            farthest = max(farthest, i + nums[i])
            if i == current_end:
                jumps += 1
                current_end = farthest

        return jumps
```

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var jump = function(nums) {
    let jumps = 0;
    let currentEnd = 0;
    let farthest = 0;

    for (let i = 0; i < nums.length - 1; i++) {   // stop at n-2
        farthest = Math.max(farthest, i + nums[i]);
        if (i === currentEnd) {
            jumps++;
            currentEnd = farthest;
        }
    }

    return jumps;
};
```

---

## Jump Game I vs Jump Game II

| | #55 Jump Game | #45 Jump Game II |
|---|---|---|
| Goal | Can you reach the end? | Minimum jumps to reach the end |
| Return | `boolean` | `number` |
| Key variable | `max_reach` | `current_end` + `farthest` |
| Jump trigger | Never (no jump counting) | When `i == current_end` |
| Loop range | `0` to `n-1` | `0` to `n-2` |

Both use the same `max(farthest, i + nums[i])` update — Jump Game II just counts how many times you're forced to extend to a new layer.

---

## Edge Cases

- [x] Single element → `[0]` → loop never runs → 0 jumps
- [x] Two elements → `[1,0]` → i=0 hits `current_end`, jumps=1 → return 1
- [x] Already at end → n=1 → `range(0)` is empty → 0 jumps
- [x] Large jumps → `[100, ...]` → one jump covers everything
- [x] Minimum jumps path ≠ always taking the biggest jump (e.g. take a medium jump that lands on a large value)

---

## Notes & Key Takeaways

- Loop to `n - 2`, not `n - 1`. The last element never triggers a jump — we only need to *reach* it.
- `farthest` accumulates the best reach across the entire current level before committing to the next jump. This is what makes it greedy-optimal.
- When `i == current_end` the jump is unavoidable — you've exhausted all positions reachable with the previous number of jumps.

---

## Related Problems

| # | Problem | Difference |
|---|---------|------------|
| 55 | Jump Game | Boolean reachability, no jump count |
| 1306 | Jump Game III | Jump ±nums[i], backwards allowed |
| 1345 | Jump Game IV | Jump to same-value indices, BFS |
