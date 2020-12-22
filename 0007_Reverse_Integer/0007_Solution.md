# Reverse Integer

[Easy] — https://leetcode.com/problems/reverse-integer/

## Problem

### Description

Given a 32-bit signed integer, reverse digits of an integer.

**Note:**
Assume we are dealing with an environment that could only store integers within the 32-bit signed integer range: $-2^{31}, 2^{31} - 1$. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

### Examples

```
Input: x = 123
Output: 321
```

```
Input: x = -123
Output: -321
```

```
Input: x = 120
Output: 21
```

```
Input: x = 0
Output: 0
```

### Constraints

- `-2^31 <= x <= 2^31 - 1`

## Procedure

### General idea

The reverse operation is quite simple. We regard the integer as a stack, popping the last digit from the original integer, and inserting it as the last digit to the new integer.
$$
\begin{array}{lll} 
	\overline{a_0\ a_1\ a_2\ a_3 \ \cdots \ a_{n-4} \ a_{n-3} \ a_{n-2} \ a_{n - 1}} & & \\
	\\
	\overline{a_0\ a_1\ a_2\ a_3 \ \cdots \ a_{n-4} \ a_{n-3} \ a_{n - 2}} & \stackrel{a_{n - 1}}{\longrightarrow} & \\
	\\
	\overline{a_0\ a_1\ a_2\ a_3 \ \cdots \ a_{n-4} \ a_{n-3}} & \stackrel{a_{n-2}}{\longrightarrow} & \overline{a_{n-1}} \\
	\\
	\overline{a_0\ a_1\ a_2\ a_3 \ \cdots \ a_{n-4}} & \stackrel{a_{n-3}}{\longrightarrow} & \overline{a_{n-1}\ a_{n-2}} \\
	\\
	& \ \ \ \vdots & \\
	\\
	\overline{a_0\ a_1} & \stackrel{a_{2}}{\longrightarrow} & \overline{a_{n - 1}\ a_{n - 2} \ a_{n - 3}\ a_{n - 4} \ \cdots \ a_{3}} \\
	\\
	\overline{a_0} & \stackrel{a_{1}}{\longrightarrow} & \overline{a_{n - 1}\ a_{n - 2} \ a_{n - 3}\ a_{n - 4} \ \cdots \ a_{3} \ a_2}  \\
	\\
	& \stackrel{a_{0}}{\longrightarrow} & \overline{a_{n - 1}\ a_{n - 2} \ a_{n - 3}\ a_{n - 4} \ \cdots \ a_{3} \ a_2\ a_1} \\
    \\ 
    & & \overline{a_{n - 1}\ a_{n - 2} \ a_{n - 3}\ a_{n - 4} \ \cdots \ a_{3} \ a_2\ a_1\ a_0}
\end{array}
$$
Then we need to check whether the new integer overflows.

If the limit of the integers has $l$ digits, any integer less than $l$ digits cannot overflow. Since the input number is in the range of the limit, then the digits of the input number $n \le l$. Therefore, we may find that $\overline{a_{n-1}}$, $\overline{a_{n-1}\ a_{n - 2}}$, $\cdots$, $\overline{a_{n - 1}\ a_{n - 2} \ a_{n - 3}\ a_{n - 4} \ \cdots \ a_{3} \ a_2\ a_1}$ cannot overflow, and we just need to judge whether the integer will overflow in the last step.

### Basic Algorithm

Assume the input number `x` has `n` digits

Initialize `res = 0`, `digit = 0`

Iterate until `x = 0`

- `res = 10 * res + digit`
- `digit = x mod 10`, `x = floor(x / 10)`

Check whether the result overflows

- Yes — return `0`
- No — return `10 * res + digit`


### Extra discussion

The algorithm is extensible for changed limits, as long as the input number is in the range of the limit. Moreover, for positive integer, it will only compare with the positive limit, and for negative integer, it will only compare with the negative limit. Therefore, the positive limit and the negative limit don’t interfere with each other, and they could have different number of digits.

We check over at the last step, but we cannot directly calculate the final result and compare it with the limit, because we cannot create an integer that overflows. Therefore, we check overflow before getting the final result. We compare top $l-1$ digits and the last digit separately, and then get the final result only if we confirm that it will not overflow.

## Solution

```c++
class Solution {
public:
    int reverse(int x) {
        int res = 0;
        int digit= 0;
        while (x != 0) {
            res = 10 * res + digit;
            digit = x % 10;
            x = x / 10;
        }
        if (digit < 0) {
            if (INT_MIN / 10 > res)
                return 0;
            if (INT_MIN / 10 == res && INT_MIN % 10 > digit)
                return 0;
        } else {
            if (INT_MAX / 10 < res)
                return 0;
            if (INT_MAX / 10 == res && INT_MAX % 10 < digit)
                return 0;
        }
        res = 10 * res + digit;
        return res;
    }
};
```

