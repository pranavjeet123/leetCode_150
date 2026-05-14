# 88 - Merge Sorted Array

**Difficulty:** Easy  
**LeetCode Link:** [#88](https://leetcode.com/problems/merge-sorted-array/)  
**Category:** Array / String  
**Status:** ✅ Solved

---

## Problem Statement

> You are given two integer arrays `nums1` and `nums2`, sorted in non-decreasing order, and two integers `m` and `n`, representing the number of elements in `nums1` and `nums2` respectively.
>
> Merge `nums1` and `nums2` into a single array sorted in non-decreasing order.
>
> The final sorted array should **not** be returned by the function, but instead be stored inside the array `nums1`. To accommodate this, `nums1` has a length of `m + n`, where the first `m` elements denote the elements that should be merged, and the last `n` elements are set to `0` and should be ignored. `nums2` has a length of `n`.

**Example 1:**
```
Input:  nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: [1,2,2,3,5,6]
Explanation: Merging [1,2,3] and [2,5,6] gives [1,2,2,3,5,6].
```

**Example 2:**
```
Input:  nums1 = [1], m = 1, nums2 = [], n = 0
Output: [1]
Explanation: Merging [1] and [] gives [1].
```

**Example 3:**
```
Input:  nums1 = [0], m = 0, nums2 = [1], n = 1
Output: [1]
Explanation: m = 0, so nums1 has no real elements. Result is [1].
```

**Constraints:**
- `nums1.length == m + n`
- `nums2.length == n`
- `0 <= m, n <= 200`
- `1 <= m + n <= 200`
- `-10^9 <= nums1[i], nums2[j] <= 10^9`

---

## Approach

**Strategy:** Three Pointers — Fill from the Back

**Intuition:**  
Merging from the front would force us to shift elements right to make room, costing O(m·n) in the worst case. Instead, fill `nums1` from the **back**: the tail of `nums1` is empty (`0`-padded), so placing the largest element there is always safe and requires no shifting.

**Steps:**
1. Start pointer `p1 = m - 1` (last real element of `nums1`).
2. Start pointer `p2 = n - 1` (last element of `nums2`).
3. Start write pointer `write = m + n - 1` (last slot of `nums1`).
4. While `p2 >= 0`:
   - If `p1 >= 0` and `nums1[p1] > nums2[p2]`, place `nums1[p1]` at `write`, decrement `p1`.
   - Otherwise, place `nums2[p2]` at `write`, decrement `p2`.
   - Decrement `write`.
5. Any remaining elements of `nums2` (when `p1` exhausted first) are already placed. No action needed for leftover `nums1` elements — they are already in place.

```
nums1: [1, 2, 3, 0, 0, 0]   m=3
nums2: [2, 5, 6]             n=3

Step 1: compare 3 vs 6 → place 6 at index 5  → [1,2,3,0,0,6]
Step 2: compare 3 vs 5 → place 5 at index 4  → [1,2,3,0,5,6]
Step 3: compare 3 vs 2 → place 3 at index 3  → [1,2,3,3,5,6]  ← wait, p1 was at index 2 (value=3)
        Actually: place nums1[2]=3 → write=3  → [1,2,3,3,5,6], p1=1
Step 4: compare 2 vs 2 → nums2[p2]=2 wins (or equal, pick nums2) → place 2 at index 2 → [1,2,2,3,5,6], p2=-1
Done.  p2 < 0 → stop.   Result: [1,2,2,3,5,6] ✓
```

**Time Complexity:** O(m + n) — each element is written exactly once  
**Space Complexity:** O(1) — in-place, no extra array

---

## Solution

```python
from typing import List

class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        p1 = m - 1
        p2 = n - 1
        write = m + n - 1

        while p2 >= 0:
            if p1 >= 0 and nums1[p1] > nums2[p2]:
                nums1[write] = nums1[p1]
                p1 -= 1
            else:
                nums1[write] = nums2[p2]
                p2 -= 1
            write -= 1
```

```javascript
/**
 * @param {number[]} nums1
 * @param {number} m
 * @param {number[]} nums2
 * @param {number} n
 * @return {void} — modifies nums1 in-place
 */
var merge = function(nums1, m, nums2, n) {
    let p1 = m - 1;
    let p2 = n - 1;
    let write = m + n - 1;

    while (p2 >= 0) {
        if (p1 >= 0 && nums1[p1] > nums2[p2]) {
            nums1[write] = nums1[p1];
            p1--;
        } else {
            nums1[write] = nums2[p2];
            p2--;
        }
        write--;
    }
};
```

---

## Edge Cases

- [x] `n = 0` — `nums2` is empty, `nums1` is already sorted, loop never runs
- [x] `m = 0` — `p1 = -1` immediately, so all `nums2` elements are copied into `nums1`
- [x] All `nums2` elements are larger than all `nums1` elements
- [x] All `nums1` elements are larger than all `nums2` elements
- [x] Duplicate values across both arrays

---

## Notes & Key Takeaways

- The **fill-from-back** trick is the key insight. It avoids the need for a separate output array and eliminates element shifting.
- When `p1 < 0` (nums1 exhausted), the `else` branch safely drains the rest of `nums2`.
- When `p2 < 0` (nums2 exhausted), leftover `nums1` elements are already in the correct positions — no extra work needed.
- Equal elements: we prefer placing from `nums2` first (the `else` branch) — both orderings are correct since the values are equal.

---

## Related Problems

| # | Problem | Similarity |
|---|---------|------------|
| 21 | Merge Two Sorted Lists | Same two-pointer merge, on linked lists |
| 23 | Merge k Sorted Lists | Generalises merge to k arrays using a heap |
| 912 | Sort an Array | General sorting without the sorted-input guarantee |
