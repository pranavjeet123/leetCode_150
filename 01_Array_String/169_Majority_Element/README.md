# 169 - Majority Element

**Difficulty:** Easy  
**LeetCode Link:** [#169](https://leetcode.com/problems/majority-element/)  
**Category:** Array / String  
**Status:** ✅ Solved

---

## Problem Statement

> Given an array `nums` of size `n`, return the **majority element** — the element that appears more than `⌊n/2⌋` times. A majority element is guaranteed to exist.

**Example 1:**
```
Input:  nums = [3,2,3]
Output: 3
```

**Example 2:**
```
Input:  nums = [2,2,1,1,1,2,2]
Output: 2
```

**Constraints:**
- `1 <= n <= 5 * 10^4`
- `-10^9 <= nums[i] <= 10^9`
- A majority element always exists.

---

## Three Approaches

### Approach 1 — HashMap
**Time:** O(n) · **Space:** O(n)

Count frequencies; return the element with count `> n/2`. Simple but uses extra space.

---

### Approach 2 — Sort
**Time:** O(n log n) · **Space:** O(1)

If the majority element appears `> n/2` times, it **must occupy the middle index** of the sorted array. Return `nums[n//2]`.

```
[2,2,1,1,1,2,2] → sorted → [1,1,1,2,2,2,2]
                                     ↑ index 3 = n//2 = 2 ✓
```

---

### Approach 3 — Boyer-Moore Voting ✅ (Follow-up: O(n) time, O(1) space)

**Intuition:**  
Pair each non-majority element with one majority element and cancel them out. Since the majority appears more than all others combined, it always survives.

Maintain:
- `candidate` — current surviving element
- `count` — its "lead" over all other elements

For each number:
- If `count == 0` → no current survivor, elect this number as new `candidate`
- If `num == candidate` → same side, `count++`
- Else → opposing vote, `count--`

The final `candidate` is always the majority element.

**Why it's correct:** The majority element has `> n/2` votes. Even if every other element voted against it optimally, it can cancel at most `n/2 - 1` majority votes — leaving at least 1 net vote remaining.

**Dry run — Example 2:** `[2, 2, 1, 1, 1, 2, 2]`
```
Start: candidate=None, count=0

num=2: count=0 → candidate=2, count=1
num=2: 2==candidate → count=2
num=1: 1≠candidate → count=1
num=1: 1≠candidate → count=0
num=1: count=0 → candidate=1, count=1
num=2: 2≠candidate → count=0
num=2: count=0 → candidate=2, count=1

Return candidate = 2 ✓
```

Notice how the majority element `2` (4 occurrences) kept reclaiming candidacy even after being knocked out.

**Time Complexity:** O(n) — single pass  
**Space Complexity:** O(1)

---

## Solution

```python
from typing import List

class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        candidate, count = None, 0

        for num in nums:
            if count == 0:
                candidate = num
            count += 1 if num == candidate else -1

        return candidate
```

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var majorityElement = function(nums) {
    let candidate = null;
    let count = 0;

    for (const num of nums) {
        if (count === 0) candidate = num;
        count += (num === candidate) ? 1 : -1;
    }

    return candidate;
};
```

---

## Comparison of All Approaches

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| HashMap | O(n) | O(n) | Easiest to reason about |
| Sort | O(n log n) | O(1) | Clean but not linear |
| **Boyer-Moore** | **O(n)** | **O(1)** | Optimal — answers the follow-up |

---

## Edge Cases

- [x] Single element → `[5]` → `count=0` → `candidate=5`, `count=1` → return `5`
- [x] All same → `[3,3,3]` → count only goes up → return `3`
- [x] Majority at the end → `[1,2,2,2]` → 1 and 2 cancel, then `2` takes over → return `2`
- [x] Negative numbers → comparison is value-based, works identically

---

## Notes & Key Takeaways

- Boyer-Moore only works when a majority element is **guaranteed** to exist. Without that guarantee, a second pass to verify the candidate is needed.
- The trick `count += 1 if num == candidate else -1` collapses the three-branch logic into one line.
- The algorithm doesn't need to know `n` in advance — it works on a stream.

---

## Related Problems

| # | Problem | Similarity |
|---|---------|------------|
| 229 | Majority Element II | Elements appearing > n/3 times — two candidates |
| 1150 | Check If a Number Is Majority Element | Verify majority in sorted array with binary search |
