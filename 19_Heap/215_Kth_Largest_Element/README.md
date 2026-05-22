# 215 - Kth Largest Element in an Array

**Difficulty:** Medium  
**LeetCode Link:** [#215](https://leetcode.com/problems/kth-largest-element-in-an-array/)  
**Category:** Heap  
**Status:** ✅ Solved

---

## Problem Statement

> Given an integer array `nums` and an integer `k`, return the **kth largest** element in the array. Note: kth largest in sorted order, not kth distinct. Can you solve it without sorting?

**Example 1:**
```
Input:  nums = [3,2,1,5,6,4], k = 2
Output: 5
```

**Example 2:**
```
Input:  nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```

**Constraints:**
- `1 <= k <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

---

## Two Approaches

### Approach 1 — Min-Heap of Size k
**Time:** O(n log k) · **Space:** O(k)

Keep a min-heap capped at `k` elements. After processing all numbers, the root (minimum of the heap) is the kth largest overall.

**Why?** A heap of size `k` always holds the `k` largest elements seen so far. The smallest of those `k` is the answer.

```
nums = [3,2,1,5,6,4], k=2

push 3 → heap=[3]
push 2 → heap=[2,3]
push 1 → heap=[2,3]  → size>k → pop min(1) → heap=[2,3]  wait...
```

Actually: push then pop when size > k:
```
push 3 → [3],       size=1
push 2 → [2,3],     size=2
push 1 → [1,2,3],   size=3 > k → pop 1 → [2,3]
push 5 → [2,3,5],   size=3 > k → pop 2 → [3,5]
push 6 → [3,5,6],   size=3 > k → pop 3 → [5,6]
push 4 → [4,5,6],   size=3 > k → pop 4 → [5,6]
Return heap[0] = 5 ✓
```

---

### Approach 2 — QuickSelect ✅ (Optimal for "without sorting")
**Time:** O(n) average, O(n²) worst · **Space:** O(1)

QuickSelect is a partial quicksort — partition around a pivot, then **only recurse into the side that contains the target index**. With a random pivot it achieves O(n) average.

**Key mapping:** kth largest = index `(n - k)` in sorted ascending order. Find that index using partitioning.

**How the partition works:**
```
Choose a pivot. Rearrange so that:
  all elements < pivot → left of partition point p
  pivot itself         → position p
  all elements > pivot → right of p
```

After partitioning:
- If `p == target` → found it
- If `p < target`  → answer is in the right half
- If `p > target`  → answer is in the left half

**Dry run — Example 1:** `[3,2,1,5,6,4]`, k=2 → target = 6-2 = 4
```
Partition around pivot=4 (last element):
  [3,2,1,_,_,4]
  i=0: 3<4 → swap(0,p=0) → p=1
  i=1: 2<4 → swap(1,p=1) → p=2
  i=2: 1<4 → swap(2,p=2) → p=3
  i=3: 5>4 → skip
  i=4: 6>4 → skip
  place pivot: swap(p=3, hi=5) → [3,2,1,4,6,5], p=3

  p=3 < target=4 → recurse right: [6,5], lo=4, hi=5

Partition [6,5] around pivot=5:
  i=4: 6>5 → skip
  place pivot: swap(p=4, hi=5) → [6,5] becomes [5,6], p=4

  p=4 == target=4 → return nums[4] = 5 ✓
```

---

## Solution

```python
import heapq
import random
from typing import List

class Solution:

    # Approach 1: Min-Heap — O(n log k) time, O(k) space
    def findKthLargest_heap(self, nums: List[int], k: int) -> int:
        heap = []
        for num in nums:
            heapq.heappush(heap, num)
            if len(heap) > k:
                heapq.heappop(heap)
        return heap[0]

    # Approach 2: QuickSelect — O(n) average, O(1) space
    def findKthLargest(self, nums: List[int], k: int) -> int:
        target = len(nums) - k

        def partition(lo, hi):
            # Random pivot avoids O(n²) worst case on sorted input
            pivot_idx = random.randint(lo, hi)
            nums[pivot_idx], nums[hi] = nums[hi], nums[pivot_idx]

            pivot = nums[hi]
            p = lo
            for i in range(lo, hi):
                if nums[i] <= pivot:
                    nums[i], nums[p] = nums[p], nums[i]
                    p += 1
            nums[p], nums[hi] = nums[hi], nums[p]
            return p

        lo, hi = 0, len(nums) - 1
        while lo <= hi:
            p = partition(lo, hi)
            if p == target:
                return nums[p]
            elif p < target:
                lo = p + 1
            else:
                hi = p - 1
```

```javascript
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number}
 */

// Approach 1: Min-Heap simulation — O(n log k)
var findKthLargest_heap = function(nums, k) {
    // JS has no built-in heap; simulate with a sorted array for clarity
    // In interviews, state you'd use a priority queue
    const heap = [];
    const push = (val) => {
        heap.push(val);
        heap.sort((a, b) => a - b);        // min-heap: smallest at index 0
        if (heap.length > k) heap.shift();
    };
    for (const num of nums) push(num);
    return heap[0];
};

// Approach 2: QuickSelect — O(n) average, O(1) space
var findKthLargest = function(nums, k) {
    const target = nums.length - k;

    const partition = (lo, hi) => {
        const pivotIdx = lo + Math.floor(Math.random() * (hi - lo + 1));
        [nums[pivotIdx], nums[hi]] = [nums[hi], nums[pivotIdx]];

        const pivot = nums[hi];
        let p = lo;
        for (let i = lo; i < hi; i++) {
            if (nums[i] <= pivot) {
                [nums[i], nums[p]] = [nums[p], nums[i]];
                p++;
            }
        }
        [nums[p], nums[hi]] = [nums[hi], nums[p]];
        return p;
    };

    let lo = 0, hi = nums.length - 1;
    while (lo <= hi) {
        const p = partition(lo, hi);
        if (p === target)     return nums[p];
        else if (p < target)  lo = p + 1;
        else                  hi = p - 1;
    }
};
```

---

## Comparison

| Approach | Time (avg) | Time (worst) | Space | Without sorting? |
|----------|------------|--------------|-------|-----------------|
| Sort + index | O(n log n) | O(n log n) | O(1) | ❌ |
| Min-Heap size k | O(n log k) | O(n log k) | O(k) | ✅ |
| **QuickSelect** | **O(n)** | O(n²) | **O(1)** | ✅ |

> In practice, use the **heap** for streaming data or when the array can't be mutated. Use **QuickSelect** when you own the array and want O(n) average.

---

## Edge Cases

- [x] `k = 1` → return the maximum element
- [x] `k = n` → return the minimum element
- [x] All elements identical → `[2,2,2]`, k=2 → return 2
- [x] Negative numbers → works identically, comparisons are value-based
- [x] Already sorted input → random pivot prevents O(n²) degeneration

---

## Notes & Key Takeaways

- **Heap approach** is easier to implement correctly and sufficient for most interviews. State the trade-off.
- **QuickSelect** answers the "without sorting" follow-up. The random pivot is critical — a fixed pivot (e.g. always `nums[hi]`) degrades to O(n²) on sorted input.
- The target index mapping: kth largest → index `n - k` in 0-indexed ascending order.
- The iterative version of QuickSelect (using `lo`/`hi` loop instead of recursion) avoids stack overflow on large inputs.

---

## Related Problems

| # | Problem | Similarity |
|---|---------|------------|
| 347 | Top K Frequent Elements | K-largest by frequency, use heap |
| 373 | Find K Pairs with Smallest Sums | K-smallest pairs, use heap |
| 295 | Find Median from Data Stream | Two heaps for streaming median |
