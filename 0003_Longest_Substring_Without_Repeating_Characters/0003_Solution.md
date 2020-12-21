# Longest Substring Without Repeating Characters

[Medium] — https://leetcode.com/problems/longest-substring-without-repeating-characters/

## Problem

### Description

Given a string `s`, find the length of the **longest substring** without repeating characters.

### Examples

```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```

```
Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

```
Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

```
Input: s = ""
Output: 0
```

### Constraints

- `0 <= s.length <= 5 * 104`
- `s` consists of English letters, digits, symbols and spaces.

## Procedure

### General idea

At first, we introduce a method for storing a sub-string, that is, two-pointer (two-index) method. We use $i, j$ two indexes, pointing to the leftmost character and the rightmost character of the sub-string.
$$
s_0\ s_1\ \cdots s_{i - 1}\ \underbrace{\ s_i\ s_{i + 1}\ \cdots \ s_{j - 1} \ s_j} _{j - i + 1\ \text{characters}} \ s_{j+1} \ \cdots \ s_{n - 2} \ s_{n-1}
$$
Generally, we let $i$ points to the leftmost character, and $j$ points to the rightmost character.

Then we apply the **sliding window** algorithm, an extension of dynamic programming.

Initially we set $i = j = 0$, meaning that our sub-string is only the first character.

Then we start to increment $j$, and check whether the expanded sub-string violate the rule. When we find that the newly inserted character is an repeating characters, we increase $i$ until the reduced sub-string follows the rule again. The following process shows the procedure of $i' \to i, j' \to j$ with the rule is violated during inserting the character.
$$
\begin{array}{l}
	|s_{i'}\ \cdots \ s_{k} \cdots \ s_{j'}| \leftarrow s_{j} \\
	\qquad \downarrow j = j' + 1 \\
	|s_{i'}\ \cdots \ s_{k} \cdots \ s_{j'}\ s_{j} | \\
	\qquad \downarrow s_j = s_k \ \text{violate}\\
	s_{i'} \ \cdots \ s_{k} | s_{i} \cdots s_{j'} \ s_{j} | \\
	\qquad \downarrow i = k + 1 \\
	|s_{i} \cdots s_{j}|
\end{array}
$$


We proceed this operation until $j$ reaches the end.

We notice that $i, j$ represents the longest sub-string without repeating characters with its end $s[j]$, since we always increment $i$ when the rules are violated. Because we iterate $j$ from beginning to end through the whole process, we have access to a list of $n$ longest sub-strings without repeating characters with different end characters, from $s[0]$ to $s[n-1]$. In that the ultimate longest sub-string must have an end, so it is just the longest one in the list.

### Basic algorithm

Use a hash table to check whether the new character is already in the current string.

Assume the string `s` has size `n`

Initialize `i = 0`, `len = 0` and a hash table `table`

Iterate `j` from `0` to `n - 1`

- if `s[j]` is in `table`, `i = max(table[s[j]] + 1, i)`
- insert `{s[j], j}` pair into `table`
- if `j - i + 1 > len`, then `len = j - i + 1`

After the loop, the maximum length is the length of the longest substring without repeating characters.

### Extra discussion

When we need to increase the element `i` until the range contains no repeated character, we don’t need to use a loop, but calculate instead. If the latest index of `s[j]` is lower than `i`, meaning that we don’t need to modify anything; if the latest index of `s[j]` is greater or equal than `i`, we just change `i` to the index just after the index of `s[j]`.

In C++ implementation, when we insert a key-value pair into a hash table, we cannot use `insert()` method, because it will not overwrite the value if the key has already existed. If we want to overwrite the value with the same key, we need to use `[]` operator instead.

## Solution

```c++
#include <unordered_map>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char, int> map;
        int i, j;
        int max_range = 0;
        for (i = 0, j = 0; j < int(s.size()); ++j) {
            auto pos = map.find(s[j]);
            if (pos != map.end())
                i = max(i,  pos->second + 1);
            map[s[j]] = j;
            max_range = max(max_range, j - i + 1);
        }
        return max_range;
    }
};
```

