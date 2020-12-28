# Reverse Integer

[Easy] — https://leetcode.com/problems/string-to-integer-atoi/

## Problem

### Description

Implement `atoi` which converts a string to an integer.

The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.

If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.

If no valid conversion could be performed, a zero value is returned.

**Note:**

- Only the space character `' '` is considered a whitespace character.
- Assume we are dealing with an environment that could only store integers within the 32-bit signed integer range: `[−2^31, 2^31 − 1]`. If the numerical value is out of the range of representable values, `2^31 − 1` or `−2^31` is returned.

### Examples

```
Input: str = "42"
Output: 42
```

```
Input: str = "   -42"
Output: -42
Explanation: The first non-whitespace character is '-', which is the minus sign. Then take as many numerical digits as possible, which gets 42.
```

```
Input: str = "4193 with words"
Output: 4193
Explanation: Conversion stops at digit '3' as the next character is not a numerical digit.
```

```
Input: str = "words and 987"
Output: 0
Explanation: The first non-whitespace character is 'w', which is not a numerical digit or a +/- sign. Therefore no valid conversion could be performed.
```

```
Input: str = "-91283472332"
Output: -2147483648
Explanation: The number "-91283472332" is out of the range of a 32-bit signed integer. Thefore INT_MIN (−2^31) is returned.
```

### Constraints

- `0 <= s.length <= 200`
- `s` consists of English letters (lower-case and upper-case), digits, `' '`, `'+'`, `'-'` and `'.'`.

## Procedure

### General idea

To change the input string into an integer with a lot of specifications, we just need to read it character by character, and choose one operation based on the current character, and return the final result at the end. The technique called **state machine** is used to repeat one process containing branch conditions.

We divide the program into 9 states, with state 1 initialized

- state 1 — initialization
    - if input character is `' ' `, then stay at state 1
    - if input character is `'+'`, then go to state 2
    - if input character is `'-'`, then go to state 3
    - if input character is `0` to `9`, then go to state 2
    - if input character is others, then return 0
- state 2 — positive number
    - if input character is `0` to `9`
        - if overflow occurs, then return maximum limit
        - if overflow doesn’t occur, then update number and stay at state 2
    - if input character is others, then return the current number
- state 3 — negative number
    - if input character is `0` to `9`
        - if overflow occurs, then return minimum limit
        - if overflow doesn’t occur, then update number and stay at state 2
    - if input character is others, then return the current number

### Basic Algorithm

Assume the input string `s` has `n` characters

Initialize `sign_flag = true`, `neg_flag = false`, `res = 0`

Iterate `i` from `0` to `n - 1`

- if `s[i] == '+'`, then `sign_flag = false` and `neg_flag = false`
- if `s[i] == '-'`, then `sign_flag = false` and `neg_flag = true`
- if `s[i]` is a digit character, then calculate `digit = int(s[i])`
    - if `neg_flag`
        - if overflow, then return minimum integer limit
        - if not overflow, then `res = 10 * res - digit`
    - if not `neg_flag`
        - if overflow, then return maximum integer limit
        - if not overflow, then `res = 10 * res + digit`

Return the final result is `res`

### Extra discussion

About how to detect overflow, we have discussed in [7. Reverse Integer](../0007_Reverse_Integer/0007_Solution.md). The only difference is that we need to perform the overflow detection whenever we insert one digit for simplicity.

Since the state machine only has three states, so we use a simpler implementation by flags to replace each state. In the following solution, we use `sign_flag` to control whether the program is in state 1. We use `neg_sign` to control whether the program is in state 2. Then the program will be in state 3 after passing two judgement.

In C++, a character can be treated as an integer directly, whose value will be calculated as ASCII code. Therefore, we can perform some operation faster by implementing number calculation, such as transfer one digit character `c` to its number by `d = c - 48`, since `0` has the ASCII code 48.

## Solution

```c++
#include <string>
#include <climits>
using namespace std;

class Solution {
public:
    int myAtoi(string s) {
        int res = 0;
        int digit;
        bool neg_sign = false;
        bool sign_flag = true;
        bool space_flag = true;
        int min_base = INT_MIN / 10;
        int min_digit = INT_MIN % 10;
        int max_base = INT_MAX / 10;
        int max_digit = INT_MAX % 10;
        for (char &c : s) {
            if (c == 45 && sign_flag) {
                sign_flag = false;
                neg_sign = true;
            } else if (c == 43 && sign_flag) {
                sign_flag = false;
            } else if (c == 32 && sign_flag) {
                continue;
            } else if (c >= 48 && c <= 57) {
                sign_flag = false;
                digit = c - 48;
                if (neg_sign && (res > min_base ||
                        (res == min_base && -digit >= min_digit))) {
                    res = 10 * res - digit;
                } else if (!neg_sign && (res < max_base ||
                        (res == max_base && digit <= max_digit))) {
                    res = 10 * res + digit;
                } else if (neg_sign) {
                    return INT_MIN;
                } else {
                    return INT_MAX;
                }
            } else {
                break;
            }
        }
        return res;
    }
};
```

