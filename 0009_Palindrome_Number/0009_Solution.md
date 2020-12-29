# Palindrome Integer

[Easy] — https://leetcode.com/problems/palindrome-number/

## Problem

### Description

Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.

**Follow up:** Could you solve it without converting the integer to a string?

### Examples

```
Input: x = 121
Output: true
```

```
Input: x = -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.Input: x = -123
Output: -321
```

```
Input: x = 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```

```
Input: x = -101
Output: false
```

### Constraints

- `-2^31 <= x <= 2^31 - 1`

## Procedure

### General idea

According to the requirement, all negative integers aren’t treated as palindrome integers, so we only need to consider non-negative integers.

To check whether a non-negative integer is a palindrome integers, we can just compare the original integer with its reversed integer.
$$
\left.
	\begin{array}{l} \text{original:} \quad \\ \text{reversed:} \quad \end{array}
\right.
\left|
	\begin{matrix} a_{0} \\ a_{n-1} \end{matrix}
\right.
\left|
	\begin{matrix} a_{1} \\ a_{n-2} \end{matrix}
\right.
\left|
	\begin{matrix} a_{2} \\ a_{n-3} \end{matrix}
\right|
\cdots
\left|
	\begin{matrix} a_{n-3} \\ a_{2} \end{matrix}
\right.
\left|
	\begin{matrix} a_{n-2} \\ a_{1} \end{matrix}
\right.
\left|
	\begin{matrix} a_{n-1} \\ a_{0} \end{matrix}
\right|
$$
Remind [7. Reverse Integer](../0007_Reverse_Integer/0007_Solution.md), we use almost the same algorithm to reverse the input integer. The only difference is that we don’t need to check overflow in this problem when checking whether an integer is a palindrome integer.

### Basic Algorithm

Assume the input integer is `x`

check whether `x` is non-negative integer

- if no, then return false

Initialize `original = x` and `reversed = 0`

Iterate until `x = 0`

- `digit = x % 10`
- `reversed = 10 * reversed + digit`
- `x = floor(x / 10)`

Then check whether `original = reversed`

- if yes, then return true
- if no, then return false


### Extra discussion

Although we don’t need to check overflow before calculation, if we don’t perform extra procedure, the overflow will lead to errors. Therefore, in this problem, we use `long` variable instead of `int` variable to avoid overflow, by widening the range of the variables in the program.

## Solution

```c++
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0)
            return false;
        long left = (long)x;
        long right = 0;
        while (x != 0) {
            right = 10 * right + x % 10;
            x = x / 10;
        }
        if (left != right)
            return false;
        return true;
    }
};
```

