# Longest Palindromic Substring

[Medium] — https://leetcode.com/problems/longest-palindromic-substring/

## Problem

### Description

Given a string `s`, return *the longest palindromic substring* in `s`.

### Examples

```
Input: s = "babad"
Output: "bab"
Note: "aba" is also a valid answer.
```

```
Input: s = "cbbd"
Output: "bb"
```

```
Input: s = "a"
Output: "a"
```

```
Input: s = "ac"
Output: "a"
```

### Constraints

- `1 <= s.length <= 1000`
- `s` consist of only digits and English letters (lower-case and/or upper-case)

## Procedure

### General idea

There exists an generally acknowledged optimal algorithm for find a longest palindromic substring, called **Manacher’s Algorithm**. It has only $O(n)$ time complexity, better than all normal solutions.

The core idea of the algorithm is to combine **expansion around center** and **dynamic programming** together. Since the algorithm is a bit complex, we will explain it step by step.

At first, there are two kinds of palindromic substring: containing odd-number characters like `“redivider”` and containing even-number substring like `“redder”`. It is definitely feasible to apply the algorithm to the two cases separately, but one technique to combine the two cases in order to make the code more concise — adding separator.

We add a separator to the leftmost position, the rightmost position, and any position between two characters. The separator can be any character except those could occur in the original string. For example, the string `“separator”` will be transformed as follows.

`“separator”` — `“|s|e|p|a|r|a|t|o|r|”`

Assume there is $n$ characters in the original string, then there will be $2n + 1$ characters in the new string. Denote the new string as $t$.

Then, we apply the expansion around center method to string $t$. We create a table $dp$ with $2n + 1$ to store a value corresponding to the character in $t$. The value represents the distance to the rightmost longest palindromic substring centered at that point. That is, for point $t[i]$, the longest substring centered at it has the range $t[i] - dp[i]$ to $t[i] + dp[i]$.

We can perform this step to all characters in $t$, for example

| \|   | s    | \|   | e    | \|   | p    | \|   | a    | \|   | r    | \|   | a    | \|   | t    | \|   | o    | \|   | r    | \|   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | 1    | 0    | 1    | 0    | 1    | 0    | 1    | 0    | 3    | 0    | 1    | 0    | 1    | 0    | 1    | 0    | 1    | 0    |

Then we can find the biggest number in the table, and get the longest palindromic substring. Based on cases discussion, the beginning index of the substring centered at $t[i]$ is $\left\lfloor \dfrac{dp[i] - 1}{2} \right \rfloor$ with length $dp[i]$.

It is already an algorithm, leading to a worst-case $O(n^2)$ time complexity, since each computation of the table may cost $O(n)$ complexity. In order to optimize the algorithm to linear time, we use dynamic programming — we treat the table $dp$ as the dynamic programming table, aiming to calculate the values using former states.

Before showing the algorithm, we first explain the principles, that is the longest palindromic string with its center inside another palindromic string.

Suppose the there is a palindromic sub-string range $t[l]$ to $t[r]$ with its center $t[c]$, inside the string $t$ (with separators). We call this sub-string as outer palindromic sub-string. Then we examine how to find the longest palindromic sub-string indexed at $t[i]$ where $i > c$, that is, calculate $dp[i]$.

**If $i \ge r$**, the palindromic sub-string we define doesn’t make sense, so we directly use the expansion around center method.

**If $i < r$**, due to the symmetry of the outer palindromic sub-string, we calculate the symmetrical point of $t[i]$ about $t[c]$, that is $j = 2 * c - i$. We have already calculate $dp[j]$, so we can discuss its meaning

- if $dp[j] \le j - l = r - i$, meaning that the longest palindromic sub-string centered at $t[j]$ is contained by the outer palindromic sub-string centered at $t[c]$. By the symmetry, there must be an identical palindromic sub-string centered at $t[i]$. Then we can copy the value $dp[j]$ to $dp[i]$. Since it may not be the longest palindromic sub-string, because the characters outside the outer palindromic sub-string may be different, so we need to perform expansion around center based on the copied value.
- if $dp[j] \le j - l = r - i$, meaning that the longest palindromic sub-string centered at $t[j]$ exceeds the range of the outer palindromic sub-string. Since the symmetry is valid only inside the outer palindromic sub-string, we can only ensure that there exists a palindromic sub-string indexed at $t[i]$ with its range inside the outer palindromic sub-string. Then we assign the value of $dp[i] = r - i$. Similarly, we need to perform expansion around center based on the assigned value.

In short, the procedure for the second case is summarized as $dp[i] = \min (dp[j], r - i)$ initially and then performing expansion around center.

After calculating $dp[i]$, we check whether the longest palindromic sub-string centered at $t[i]$ exceeds the outer palindromic sub-string. If it’s the case, we update the outer palindromic sub-string we use.

Then we need to clarify the initial state. It’s obvious that for any arbitrary non-empty string $s$, its transformation string $t$ must have a palindromic sub-string $t[0], t[1]. t[2]$. Therefore, we can set $dp[0] = 0, dp[1] = 1, l = 0, c = 1, r = 2$. 

Applying the dynamic programming stated above, we can calculate all values in $dp$ in $O(n)$ time, so we are able to find the longest palindromic sub-string in linear time.

### Basic Algorithm

Assume the length of the input string `s` is `n`.

Firstly add separators to the original string `s` to string `t`.

Initialize a dynamic programming table with `m = 2 * n + 1` zeros.

Initialize `center = 1`, `right_max = 2`, `start = 0`, `max_length = 1`

Iterate `i` from `1` to the end of the string

- apply dynamic programming, if `i < right_max`
    - `dp[i] = min(dp[2 * center - i], right_max - i)`
- then use expansion around center, `left = i - dp[i] `, `right = i + dp[i]`
- while `t[left] == t[right]`
    - `left = left - 1`, `right = right + 1`, `dp[i] = dp[i] + 1`
- update outer palindromic sub-string if `right > right_max`
    - `center = i`, `right_max = right`
- compare with the current longest palindromic sub-string, if `dp[i] > max_len`
    - `start = floor((i - dp[i]) / 2)`, `max_len = dp[i]`

Then the longest substring has the index range `start` to `start + max_len`.

### Extra discussion

There is one small limit for this algorithm, that is, the separator must be different from any characters in the string. In this problem, we use `|` because string $s$ only consists of digits and English characters. If the input constraint is widened, the separator may also need to be changed.

Analyzing Manacher’s algorithm, the difference with the expansion around center method exerted by the dynamic programming is that, before calculating the longest palindromic sub-string centered at one point, it will copy the value from previous point. This case only occurs when the current point $t[i]$ is inside the outer palindromic sub-string, and this is the reason why we need to update it every loop.

## Solution

```c++
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
string longestPalindrome(string s) {
        if (s.empty()) return "";
        int center = 1;
        int right_max = 2;
        int mirror;
        int left, right;
        int start = 0;
        int max_len = 1;
        string t = "|";
        for (char &c : s) {
            t += c;
            t += "|";
        }
        int size = (int)t.size();
        vector<int> dp(size, 0);
        dp[1] = 1;
        for (int i = 0; i < size; ++i) {
            if (i <= right_max) 
                dp[i] = min(right_max - i, dp[2 * center - i]);
            left = i - 1 - dp[i];
            right = i + 1 + dp[i];
            while (left >= 0 && right < size && t[left] == t[right]) {
                left -= 1;
                right += 1;
                dp[i] += 1;
            }
            if (i + dp[i] > right_max) {
                right_max = i + dp[i];
                center = i;
            }
            if (dp[i] > max_len) {
                max_len = dp[i];
                start = (i - dp[i]) / 2;
            }
        }
        return s.substr(start, max_len);
    }
};
```

