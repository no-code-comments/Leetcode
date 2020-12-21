# ZigZag Conversion

[Medium] — https://leetcode.com/problems/zigzag-conversion/

## Problem

### Description

The string `"PAYPALISHIRING"` is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

```
P   A   H   N
A P L S I I G
Y   I   R
```

And then read line by line: `"PAHNAPLSIIGYIR"`

Write the code that will take a string and make this conversion given a number of rows:

```
string convert(string s, int numRows);
```

### Examples

```
Input: s = "PAYPALISHIRING", numRows = 3
Output: "PAHNAPLSIIGYIR"
```

```
Input: s = "PAYPALISHIRING", numRows = 4
Output: "PINALSIGYAHRPI"
Explanation:
P     I    N
A   L S  I G
Y A   H R
P     I
```

```
Input: s = "A", numRows = 1
Output: "A"
```

### Constraints

- `1 <= s.length <= 1000`
- `s` consists of English letters (lower-case and upper-case), `','` and `'.'`.
- `1 <= numRows <= 1000`

## Procedure

### General idea

Denote the character by its index, the string $s$ is arranged as below.

Let $n$ be the number of rows `numRows`
$$
\left. 
	\begin{array}{c} 
        0 & & &  & 2n - 2 & & & & 4n-4\\ 
        \downarrow & & & \nearrow & \downarrow & & & \nearrow & \downarrow \\ 
        1 & &  2n - 3 & & 2n - 1 & & 4n-5 & & 4n-3 \\ 
        \downarrow & & \uparrow & & \downarrow & & \uparrow & & \downarrow \\ 
        \vdots & & \vdots & & \vdots & & \vdots & & \vdots & \cdots \\ 
        \downarrow & & \uparrow & & \downarrow & & \uparrow & & \downarrow \\ 
        n - 2 & & n & & 3n-4 & & 3n-2 & & 5n-6 \\ 
        \downarrow & \nearrow & & & \downarrow & \nearrow & & & \downarrow  \\
        n - 1 & & & & 3n-3 & & & & 5n - 5
	\end{array} 
\right.
$$
We can observe that the string follows a pattern — $ 0 \sim 2n - 3$, $2n-2 \sim 4n-5$, $\cdots$. This kind of pattern is periodic with length $l = 2n - 2$.

Therefore, we split string $s$ into several blocks according to this kind of pattern as follows.
$$
\left|
	\begin{array}{c} 
        0 & & & \\ 
        \downarrow & & & \\ 
        1 & &  2n - 3 & \\ 
        \downarrow & & \uparrow & \\ 
        \vdots & & \vdots & \\ 
        \downarrow & & \uparrow & \\ 
        n - 2 & & n & \\ 
        \downarrow & \nearrow & & \\
        n - 1 & & & 
	\end{array} 
\right|
\left. 
	\begin{array}{c} 
        2n - 2 & & & \\ 
        \downarrow & & \\ 
        2n - 1 & & 4n-5 \\ 
        \downarrow & & \uparrow \\ 
        \vdots & & \vdots \\ 
        \downarrow & & \uparrow \\ 
        3n-4 & & 3n-2 \\ 
        \downarrow & \nearrow \\
        3n - 3 & & 
	\end{array} 
\right|
\left. 
	\begin{array}{c} 
        4n-4 & & & \\ 
        \downarrow & & \\ 
        4n-3 & \\ 
        \downarrow &  \\ 
        \vdots & & \cdots \\ 
        \downarrow & \\ 
        5n-6 & \\ 
        \downarrow &\\
        5n-5 & & 
	\end{array} 
\right.
$$
Then the zigzag conversion can be viewed as

- $0 \to 2n - 2 \to \cdots$ — Line 1 of each block from left to right
- $1 \to 2n - 3 \to 2n-1 \to 4n-5 \to \cdots$ — Line 2 of each block from left to right
- $\cdots$
- $n - 1 \to 3n - 3 \to 5n-5$ — Line $n-1$ of each block from left to right

The final string is completed by connecting the above strings from up to down.

We find the start points for each blocks, which are given by mathematical expression $2k(n - 1)$.

Then we need to examine which point should be inserted for a specific line $i$. There occur two cases for one line

- Line $i = 0$ or $i = n - 1$, only one element $s[2k(n-1) + i]$ needs to be considered.
- Line $i \ne 0$ and $ i \ne n - 1$, there will be two elements $s[2k(n-1) + i], s[2k(n - 1) + n - 1 - i]$ need to be considered.

### Basic Algorithm

Assume the input string `s` has size `size` with number of rows `numRows`

Create an empty string `res`

Iterate `i` from `0` to `numRows - 1`, indicating each line

- Iterate `j` from `0` to `size` with the increment step `cycle = 2 * numRows - 2`
    - if `j + i < size`, then `res` add character `s[j + i]`
    - if `i != 0` and `i != numRows - 1`, consider the second character
        - if `j + cycle - i < size`, then `res` add character `s[j + cycle - i]`


### Extra discussion

There are only one corner case, that is number of rows $n = 1$. When this situation occurs, the characters for each block will be calculated as $2n - 2 = 0$, leading to an error. Actually one string with $n = 1$ will return the original string, so we just need to put a judgement at the beginning of the function.

We combine the case above with another situation, when the size of the string is less than number of rows $n$. In this case, we can also return the original string. Notice that our algorithm is still functional for this circumstance.

## Solution

```c++
#include <string>
using namespace std;

class Solution {
public:
    string convert(string s, int numRows) {
        int size = (int)s.size();
        if (size < numRows || numRows == 1) return s;
        string res = "";
        int left, right;
        for (int level = 0; level < numRows; ++level) {
            for (int peak = 0; peak < size; peak += 2 * numRows - 2) {
                left = peak + level;
                if (left >= size)
                    break;
                res += s[left];
                if (level == 0 || level == numRows - 1)
                    continue;
                right = peak + 2 * numRows - 2 - level;
                if (right >= size)
                    continue;
                res += s[right];
            }
        }
        return res;
    }
};
```

