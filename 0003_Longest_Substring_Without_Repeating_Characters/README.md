# Longest Substring Without Repeating Characters

[Medium]

Link — https://leetcode.com/problems/longest-substring-without-repeating-characters/

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

### Basic algorithm

We use two index `i`, `j` to store the range of the unrepeated sub-string of `s`. They are both initialized with `0`. Then the length of the string can be calculated as `j - i + 1`. We iterate the string to the end and then find the maximum length.

Use a hash table to check whether the new character is already in the current string.

Iterate `j` from `0` to `s.size() - 1`

- check whether the new character `s[j]` is in the hash table
    - Yes — increase the element `i` until the range contains unrepeated character
    - No — do nothing, the range is automatically enlarged
- insert the new character and its index into the hash table
- calculate the maximum length

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

