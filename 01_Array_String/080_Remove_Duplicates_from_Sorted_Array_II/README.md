# 80 - Remove Duplicates from Sorted Array II

**Difficulty:** Medium  
**LeetCode Link:** [#80](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)  
**Category:** Array / String  
**Status:** ✅ Solved

---

## Problem Statement

> Given an integer array `nums` sorted in non-decreasing order, remove some duplicates **in-place** such that each unique element appears **at most twice**. The relative order of the elements should be kept the same.
>
> Place the result in the first `k` elements of `nums` and return `k`. Do not allocate extra space — O(1) extra memory only.

**Example 1:**
```
Input:  nums = [1,1,1,2,2,3]
Output: 5, nums = [1,1,2,2,3,_]
```

**Example 2:**
```
Input:  nums = [0,0,1,1,1,1,2,3,3]
Output: 7, nums = [0,0,1,1,2,3,3,_,_]
```

**Constraints:**
- `1 <= nums.length <= 3 * 10^4`
- `-10^4 <= nums[i] <= 10^4`
- `nums` is sorted in non-decreasing order.

---

## Approach

**Strategy:** Two Pointers — Write pointer vs Read pointer

**Intuition:**  
Use a write pointer `k` that tracks where the next valid element should go. An element at position `i` (read pointer) is valid to keep if `nums[i] != nums[k-2]` — because if it equals the element two slots behind the write head, it would be a third (or more) occurrence of the same value. Since the array is sorted, all duplicates are adjacent, so this single comparison is sufficient.

**Steps:**
1. If the array has ≤ 2 elements, return its length — already valid by definition.
2. Set write pointer `k = 2` (first two elements are always kept as-is).
3. Iterate `i` from index `2` to end:
   - If `nums[i] != nums[k - 2]`, copy `nums[i]` to `nums[k]` and increment `k`.
   - Otherwise, skip (it's a third+ duplicate).
4. Return `k`.

**Dry run on Example 1:** `[1, 1, 1, 2, 2, 3]`
```
k=2, i=2: nums[2]=1, nums[k-2]=nums[0]=1 → equal → skip
k=2, i=3: nums[3]=2, nums[k-2]=nums[0]=1 → 2≠1 → write → [1,1,2,...], k=3
k=3, i=4: nums[4]=2, nums[k-2]=nums[1]=1 → 2≠1 → write → [1,1,2,2,...], k=4
k=4, i=5: nums[5]=3, nums[k-2]=nums[2]=2 → 3≠2 → write → [1,1,2,2,3,...], k=5
Return 5 ✓
```

**Dry run on Example 2:** `[0,0,1,1,1,1,2,3,3]`
```
k=2, i=2: nums[2]=1, nums[0]=0 → 1≠0 → write, k=3  → [0,0,1,...]
k=3, i=3: nums[3]=1, nums[1]=0 → 1≠0 → write, k=4  → [0,0,1,1,...]
k=4, i=4: nums[4]=1, nums[2]=1 → equal → skip
k=4, i=5: nums[5]=1, nums[2]=1 → equal → skip
k=4, i=6: nums[6]=2, nums[2]=1 → 2≠1 → write, k=5  → [0,0,1,1,2,...]
k=5, i=7: nums[7]=3, nums[3]=1 → 3≠1 → write, k=6  → [0,0,1,1,2,3,...]
k=6, i=8: nums[8]=3, nums[4]=2 → 3≠2 → write, k=7  → [0,0,1,1,2,3,3,...]
Return 7 ✓
```

**Time Complexity:** O(n) — single pass  
**Space Complexity:** O(1) — in-place

---

## Solution

```python
from typing import List

class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        k = 2
        for i in range(2, len(nums)):
            if nums[i] != nums[k - 2]:
                nums[k] = nums[i]
                k += 1
        return k
```

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var removeDuplicates = function(nums) {
    let k = 2;
    for (let i = 2; i < nums.length; i++) {
        if (nums[i] !== nums[k - 2]) {
            nums[k] = nums[i];
            k++;
        }
    }
    return k;
};
```

---

## Generalisation

This pattern generalises cleanly to "at most **p** occurrences":  
replace `nums[k - 2]` with `nums[k - p]`.

```python
# At most p duplicates allowed
def removeDuplicates(nums, p):
    k = p
    for i in range(p, len(nums)):
        if nums[i] != nums[k - p]:
            nums[k] = nums[i]
            k += 1
    return k
```

- `p = 1` → solves **Remove Duplicates from Sorted Array I** (#26)
- `p = 2` → solves this problem (#80)

---

## Edge Cases

- [x] All elements identical → `[1,1,1,1]` → returns `2`, `nums = [1,1,_,_]`
- [x] No duplicates → `[1,2,3]` → returns `3`, array unchanged
- [x] Array of length 1 → `[5]` → returns `1` (loop never runs, `k=2` is clamped by `min(2, len)` — actually `k` starts at 2 but `range(2, 1)` is empty, so we return `k=2`... wait)

> **Careful:** if `len(nums) == 1`, `k` starts at 2 but the loop doesn't run, so we return 2 — but the array only has 1 element. Fix: return `min(k, len(nums))`.  
> **Actually:** LeetCode guarantees `1 <= nums.length`, and the judge checks `nums[0..k-1]`, so for a 1-element array the loop is empty and we'd return `k=2` which is wrong. The guard `k = min(2, len(nums))` or an early return handles it cleanly.

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        k = min(2, len(nums))          # guard for length-1 arrays
        for i in range(2, len(nums)):
            if nums[i] != nums[k - 2]:
                nums[k] = nums[i]
                k += 1
        return k
```

- [x] Exactly two of every element → `[1,1,2,2,3,3]` → all kept, returns `6`
- [x] Negative numbers → works identically, comparison is value-based

---

## Notes & Key Takeaways

- The `nums[i] != nums[k-2]` check is the entire algorithm. No counter needed because the sorted order guarantees duplicates are consecutive.
- This is strictly more powerful than problem #26 — the same template solves both by changing `k-2` to `k-1`.
- The write pointer `k` always lags behind or equals the read pointer `i`, so in-place writes never corrupt unread data.

---

## Related Problems

| # | Problem | Similarity |
|---|---------|------------|
| 26 | Remove Duplicates from Sorted Array | Same pattern, allow at most 1 occurrence |
| 27 | Remove Element | Two-pointer write pattern, unsorted |
| 283 | Move Zeroes | Two-pointer write, keep relative order |
