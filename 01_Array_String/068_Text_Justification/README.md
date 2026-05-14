# 68 - Text Justification

**Difficulty:** Hard  
**LeetCode Link:** [#68](https://leetcode.com/problems/text-justification/)  
**Category:** Array / String  
**Status:** ✅ Solved

---

## Problem Statement

> Given an array of strings `words` and a width `maxWidth`, format the text such that each line has **exactly** `maxWidth` characters and is fully (left and right) justified.
>
> - Pack as many words as possible per line (greedy).
> - Distribute extra spaces as evenly as possible between words. If they don't divide evenly, left gaps get the extra space.
> - The **last line** is left-justified with single spaces between words and trailing spaces to fill the width.
> - A line with a **single word** is also left-justified.

**Example 1:**
```
Input:  words = ["This","is","an","example","of","text","justification."], maxWidth = 16
Output: ["This    is    an",
         "example  of text",
         "justification.  "]
```

**Example 2:**
```
Input:  words = ["What","must","be","acknowledgment","shall","be"], maxWidth = 16
Output: ["What   must   be",
         "acknowledgment  ",
         "shall be        "]
```

**Example 3:**
```
Input:  words = ["Science","is","what","we","understand","well","enough","to",
                 "explain","to","a","computer.","Art","is","everything","else","we","do"],
        maxWidth = 20
Output: ["Science  is  what we",
         "understand      well",
         "enough to explain to",
         "a  computer.  Art is",
         "everything  else  we",
         "do                  "]
```

**Constraints:**
- `1 <= words.length <= 300`
- `1 <= words[i].length <= 20`
- `1 <= maxWidth <= 100`
- `words[i].length <= maxWidth`

---

## Approach

**Strategy:** Greedy line packing + careful space distribution

The problem has two distinct sub-problems:

### Sub-problem 1 — Which words go on each line?

Greedily advance a pointer `j` from word `i`, adding words as long as they fit:
```
total length so far + 1 (space) + len(next word) <= maxWidth
```
Words `i` to `j-1` form the current line.

### Sub-problem 2 — How to space each line?

Once the words for a line are fixed, three cases:

| Case | Rule |
|------|------|
| Last line | Left-justify: join with single spaces + trailing spaces |
| Single word on line | Left-justify: word + trailing spaces |
| Middle line, multiple words | Full-justify: distribute spaces across gaps |

**Full-justify space math:**
```
total_spaces = maxWidth - sum(len(w) for w in line_words)
gaps         = num_words - 1
base_space   = total_spaces // gaps      ← every gap gets this many
extra        = total_spaces % gaps       ← first `extra` gaps get one more
```

---

## Dry Run — Example 1

`words = ["This","is","an","example","of","text","justification."]`, `maxWidth = 16`

**Line 1:** Start at "This"
```
+ "This"    → len=4
+ "is"      → 4+1+2 = 7   ≤ 16 ✓
+ "an"      → 7+1+2 = 10  ≤ 16 ✓
+ "example" → 10+1+7 = 18 > 16 ✗  stop
line_words = ["This","is","an"]
```
```
total_chars  = 4+2+2 = 8
total_spaces = 16-8  = 8
gaps         = 2
base_space   = 8//2  = 4
extra        = 8%2   = 0
→ "This" + "    " + "is" + "    " + "an"  =  "This    is    an" ✓
```

**Line 2:** Start at "example"
```
+ "example" → 7
+ "of"      → 7+1+2  = 10 ≤ 16 ✓
+ "text"    → 10+1+4 = 15 ≤ 16 ✓
+ "justif…" → 15+1+14= 30 > 16 ✗  stop
line_words = ["example","of","text"]
```
```
total_chars  = 7+2+4 = 13
total_spaces = 16-13 = 3
gaps         = 2
base_space   = 3//2  = 1
extra        = 3%2   = 1  ← first 1 gap gets +1 space
→ "example" + "  " + "of" + " " + "text" = "example  of text" ✓
```

**Line 3:** Start at "justification." — this IS the last word → last line
```
→ "justification." + "  " (padding)  = "justification.  " ✓
```

---

## Solution

```python
from typing import List

class Solution:
    def fullJustify(self, words: List[str], maxWidth: int) -> List[str]:
        result = []
        i = 0

        while i < len(words):
            # --- Step 1: greedily pack words into this line ---
            line_len = len(words[i])
            j = i + 1
            while j < len(words) and line_len + 1 + len(words[j]) <= maxWidth:
                line_len += 1 + len(words[j])
                j += 1

            line_words = words[i:j]
            num_words = j - i
            total_chars = sum(len(w) for w in line_words)
            total_spaces = maxWidth - total_chars

            # --- Step 2: format the line ---
            is_last_line   = (j == len(words))
            is_single_word = (num_words == 1)

            if is_last_line or is_single_word:
                # Left-justify: single spaces + trailing pad
                line = ' '.join(line_words)
                line += ' ' * (maxWidth - len(line))
            else:
                # Full-justify: distribute spaces across gaps
                gaps       = num_words - 1
                base_space = total_spaces // gaps
                extra      = total_spaces % gaps   # first `extra` gaps get +1

                line = ''
                for k in range(num_words - 1):
                    line += line_words[k]
                    line += ' ' * (base_space + (1 if k < extra else 0))
                line += line_words[-1]

            result.append(line)
            i = j

        return result
```

```javascript
/**
 * @param {string[]} words
 * @param {number} maxWidth
 * @return {string[]}
 */
var fullJustify = function(words, maxWidth) {
    const result = [];
    let i = 0;

    while (i < words.length) {
        // Step 1: greedily pack words into this line
        let lineLen = words[i].length;
        let j = i + 1;
        while (j < words.length && lineLen + 1 + words[j].length <= maxWidth) {
            lineLen += 1 + words[j].length;
            j++;
        }

        const lineWords  = words.slice(i, j);
        const numWords   = lineWords.length;
        const totalChars = lineWords.reduce((sum, w) => sum + w.length, 0);
        const totalSpaces = maxWidth - totalChars;

        // Step 2: format the line
        const isLastLine   = j === words.length;
        const isSingleWord = numWords === 1;
        let line = '';

        if (isLastLine || isSingleWord) {
            // Left-justify
            line = lineWords.join(' ');
            line += ' '.repeat(maxWidth - line.length);
        } else {
            // Full-justify
            const gaps      = numWords - 1;
            const baseSpace = Math.floor(totalSpaces / gaps);
            const extra     = totalSpaces % gaps;

            for (let k = 0; k < numWords - 1; k++) {
                line += lineWords[k];
                line += ' '.repeat(baseSpace + (k < extra ? 1 : 0));
            }
            line += lineWords[numWords - 1];
        }

        result.push(line);
        i = j;
    }

    return result;
};
```

---

## Edge Cases

- [x] Last line with one word → left-justify + trailing spaces
- [x] Last line with multiple words → single spaces, trailing pad (NOT full-justify)
- [x] Middle line with one word (e.g. "acknowledgment" in Ex 2) → left-justify
- [x] Words that exactly fill `maxWidth` with no extra spaces → no extra padding needed
- [x] Single word in input → treated as last line, left-justified
- [x] All words fit on one line → that one line is the last line → left-justify

---

## Notes & Key Takeaways

- The hardest part is the **three distinct cases** for line formatting — get the conditions right before writing the space math.
- `extra = total_spaces % gaps` tells you exactly how many gaps get one additional space (always the leftmost ones).
- Track `j` as the exclusive end index — `words[i:j]` is the current line, next line starts at `j`.
- Verify by counting characters: every line must be exactly `maxWidth` — a quick `assert len(line) == maxWidth` during testing catches off-by-one bugs early.

---

## Related Problems

| # | Problem | Similarity |
|---|---------|------------|
| 6 | Zigzag Conversion | String formatting with index math |
| 151 | Reverse Words in a String | Word-level string manipulation |
