# 189 - Rotate Array

**Difficulty:** Medium  
**LeetCode Link:** [#189](https://leetcode.com/problems/rotate-array/)  
**Category:** Array / String  
**Status:** ✅ Solved

---

## Problem Statement

> Given an integer array `nums`, rotate the array to the right by `k` steps, where `k` is non-negative. Modify the array **in-place**.

**Example 1:**
```
Input:  nums = [1,2,3,4,5,6,7], k = 3
Output: [5,6,7,1,2,3,4]
Explanation:
  rotate 1 step  → [7,1,2,3,4,5,6]
  rotate 2 steps → [6,7,1,2,3,4,5]
  rotate 3 steps → [5,6,7,1,2,3,4]
```

**Example 2:**
```
Input:  nums = [-1,-100,3,99], k = 2
Output: [3,99,-1,-100]
```

**Constraints:**
- `1 <= nums.length <= 10^5`
- `-2^31 <= nums[i] <= 2^31 - 1`
- `0 <= k <= 10^5`

---

## Three Approaches

### Approach 1 — Extra Array  
**Time:** O(n) · **Space:** O(n)

Place each element directly at its final position `(i + k) % n` in a new array, then copy back.

```
i=0 → position (0+3)%7 = 3
i=1 → position (1+3)%7 = 4
...
i=4 → position (4+3)%7 = 0   ← wraps around
```

Simple but uses O(n) space.

---

### Approach 2 — Cyclic Replacements  
**Time:** O(n) · **Space:** O(1)

Place each element at its target, saving the displaced element to place next. Repeat until all n elements are handled. Requires GCD tracking to avoid infinite cycles on certain inputs.

More complex to implement correctly — skipped in favour of Approach 3.

---

### Approach 3 — Triple Reverse ✅ (Optimal)  
**Time:** O(n) · **Space:** O(1)

**Intuition:**  
A right rotation by `k` moves the last `k` elements to the front. Three reversals achieve this without any extra array:

1. Reverse the **entire** array — this puts the last `k` elements at the front, but in reverse order.
2. Reverse the **first k** elements — corrects their order.
3. Reverse the **remaining n-k** elements — corrects their order.

**Dry run — Example 1:** `[1,2,3,4,5,6,7]`, k=3
```
Step 0 — normalise: k = 3 % 7 = 3

Step 1 — reverse all:     [7, 6, 5, 4, 3, 2, 1]
Step 2 — reverse [0..2]:  [5, 6, 7, 4, 3, 2, 1]
Step 3 — reverse [3..6]:  [5, 6, 7, 1, 2, 3, 4] ✓
```

**Dry run — Example 2:** `[-1,-100,3,99]`, k=2
```
Step 0 — normalise: k = 2 % 4 = 2

Step 1 — reverse all:    [99, 3, -100, -1]
Step 2 — reverse [0..1]: [3, 99, -100, -1]
Step 3 — reverse [2..3]: [3, 99, -1, -100] ✓
```

**Why `k % n` first?**  
Rotating n elements by n steps returns the array unchanged. So k=10 on a 7-element array is the same as k=3. Without `% n`, k could exceed array bounds.

---

## Solution

```python
from typing import List

class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        n = len(nums)
        k %= n

        def reverse(lo, hi):
            while lo < hi:
                nums[lo], nums[hi] = nums[hi], nums[lo]
                lo += 1
                hi -= 1

        reverse(0, n - 1)   # reverse entire array
        reverse(0, k - 1)   # reverse first k elements
        reverse(k, n - 1)   # reverse remaining n-k elements
```

```javascript
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {void} — modifies nums in-place
 */
var rotate = function(nums, k) {
    const n = nums.length;
    k %= n;

    const reverse = (lo, hi) => {
        while (lo < hi) {
            [nums[lo], nums[hi]] = [nums[hi], nums[lo]];
            lo++;
            hi--;
        }
    };

    reverse(0, n - 1);   // reverse entire array
    reverse(0, k - 1);   // reverse first k elements
    reverse(k, n - 1);   // reverse remaining n-k elements
};
```

---

## Comparison of All Approaches

| Approach | Time | Space | In-place |
|----------|------|-------|----------|
| Brute force (rotate one step at a time) | O(n·k) | O(1) | ✅ |
| Extra array | O(n) | O(n) | ❌ |
| Cyclic replacements | O(n) | O(1) | ✅ |
| **Triple reverse** | **O(n)** | **O(1)** | **✅** |

---

## Edge Cases

- [x] `k = 0` → `k % n = 0`, all three reverses produce no change
- [x] `k >= n` → `k % n` normalises it (e.g. k=7 on length-7 array → no rotation)
- [x] `k = n` → same as k=0
- [x] Single element array → `k % 1 = 0`, no-op
- [x] Two elements → swap is just one reversal step

---

## Notes & Key Takeaways

- Always normalise with `k %= n` first — forgetting this causes index-out-of-bounds on `reverse(0, k-1)` when k > n.
- The triple-reverse trick is a classic and appears in string rotation problems too (e.g. rotate a string left by k).
- Left rotation by k = right rotation by n-k — useful to know for variants.

---

## Related Problems

| # | Problem | Similarity |
|---|---------|------------|
| 186 | Reverse Words in a String II | Triple reverse on words instead of elements |
| 61 | Rotate List | Same concept on a linked list |
| 796 | Rotate String | Check if one string is a rotation of another |
