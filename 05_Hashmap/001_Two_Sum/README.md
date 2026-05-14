# 1 - Two Sum

**Difficulty:** Easy  
**LeetCode Link:** [#1](https://leetcode.com/problems/two-sum/)  
**Category:** Hashmap  
**Status:** ✅ Solved

---

## Problem Statement

> Given an array of integers `nums` and an integer `target`, return indices of the two numbers such that they add up to `target`. You may assume that each input would have **exactly one solution**, and you may not use the same element twice. You can return the answer in any order.

**Example 1:**
```
Input:  nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: nums[0] + nums[1] = 2 + 7 = 9
```

**Example 2:**
```
Input:  nums = [3,2,4], target = 6
Output: [1,2]
```

**Example 3:**
```
Input:  nums = [3,3], target = 6
Output: [0,1]
```

**Constraints:**
- `2 <= nums.length <= 10^4`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`
- Only one valid answer exists.

---

## Approach

**Strategy:** Hashmap (One-pass)

**Intuition:**  
For each number `x`, the complement we need is `target - x`. Instead of scanning the whole array again (O(n²)), store each number's index in a hashmap as we go. If the complement is already in the map, we're done.

**Steps:**
1. Create an empty hashmap `seen = {}`.
2. Iterate through `nums` with index `i` and value `x`.
3. Compute `complement = target - x`.
4. If `complement` is in `seen`, return `[seen[complement], i]`.
5. Otherwise, store `seen[x] = i` and continue.

**Time Complexity:** O(n)  
**Space Complexity:** O(n)

---

## Solution

```python
from typing import List

class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        seen = {}
        for i, x in enumerate(nums):
            complement = target - x
            if complement in seen:
                return [seen[complement], i]
            seen[x] = i
```

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
    const seen = new Map();
    for (let i = 0; i < nums.length; i++) {
        const complement = target - nums[i];
        if (seen.has(complement)) {
            return [seen.get(complement), i];
        }
        seen.set(nums[i], i);
    }
};
```

---

## Edge Cases

- [x] Two identical numbers that sum to target → `[3,3], target=6` → `[0,1]`
- [x] Negative numbers → `[-1,-2,-3,-4,-5], target=-8` → `[2,4]`
- [x] Minimum array size (2 elements)

---

## Notes & Key Takeaways

- The one-pass hashmap is O(n) vs the brute-force O(n²) nested loop.
- Storing value→index (not index→value) is the key — we look up by complement value.
- Do NOT check `seen[x] = i` before checking the complement, or you'd allow using the same element twice.

---

## Related Problems

| # | Problem | Similarity |
|---|---------|------------|
| 167 | Two Sum II - Input Array Is Sorted | Same idea, sorted input → two pointers |
| 15 | 3Sum | Extension to three numbers |
| 18 | 4Sum | Extension to four numbers |
