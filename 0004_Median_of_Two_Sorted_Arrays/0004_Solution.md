Median of Two Sorted Arrays

[Hard] — https://leetcode.com/problems/longest-substring-without-repeating-characters/

## Problem

### Description

Given two sorted arrays `nums1` and `nums2` of size `m` and `n` respectively, return **the median** of the two sorted arrays.

**Follow up:** The overall run time complexity should be `O(log(m+n))`.

### Examples

```
Input: nums1 = [1,3], nums2 = [2]
Output: 2.00000
Explanation: merged array = [1,2,3] and median is 2.
```

```
Input: nums1 = [1,2], nums2 = [3,4]
Output: 2.50000
Explanation: merged array = [1,2,3,4] and median is (2 + 3) / 2 = 2.5.
```

```
Input: nums1 = [0,0], nums2 = [0,0]
Output: 0.00000
```

```
Input: nums1 = [], nums2 = [1]
Output: 1.00000
```

```
Input: nums1 = [2], nums2 = []
Output: 2.00000
```

### Constraints

- `nums1.length == m`
- `nums2.length == n`
- `0 <= m <= 1000`
- `0 <= n <= 1000`
- `1 <= m + n <= 2000`
- `-106 <= nums1[i], nums2[i] <= 106`

## Procedure

### Basic algorithm

At first, we discuss median itself, instead of the problem. For a given sorted array $A$ containing $k$ elements $a_0, a_1, \cdots , a_{k-1}$, we can make a separation as follows

$$ a_0, a_1, \cdots , a_{i - 1}\quad  | \quad a_i, a_{i+1}, \cdots, a_{k - 1} $$

We define that this separation follows the property: the left side has at most one more element than the right side. 

If $k$ is odd, the median is $a_{i-1}$; if $k$ is even, the median is $\dfrac{a_i + a_{i-1}}{2}$. Based on mathematics, we know that the left size has $ \left\lfloor \dfrac{k + 1}{2} \right\rfloor $ elements, and $i = \left\lfloor \dfrac{k + 1}{2} \right\rfloor $. Furthermore, the formula for the median is derived as

$$ M = \left\{ \begin{array}{ll} a_{i - 1} & k = 2t - 1, t \in \mathbb{N^*} \\ \\ \dfrac{a_i + a_{i-1}}{2} & k = 2t, t \in \mathbb{N^*} \end{array} \right. \quad i = \left\lfloor \dfrac{k + 1}{2} \right\rfloor $$

Since we cannot directly use the index in this problem when considering two arrays, so we back to the definition of separation. We make two separation line for two arrays $X, Y$ with $m$ elements and $n$ elements respectively. ($m, n \in \mathbb{N}$)

$$ \left. \begin{array}{l} x_0, x_1, \cdots, x_{i-1} \\ y_0, y_1, \cdots, y_{j-1} \end{array} \quad \right \vert \quad \left. \begin{array}{l} x_{i}, x_{i+1}, \cdots, x_{m-1} \\ y_{j}, y_{j+1}, \cdots, y_{n-1} \end{array} \quad \right. $$

We make sure that the left side has $\left\lfloor \dfrac{m + n + 1}{2} \right\rfloor$ elements, meaning that $i + j = \left\lfloor \dfrac{m + n + 1}{2} \right\rfloor$. 

If we can prove that all elements in the left side is lower than the elements in the right side, then we can find the median. Since the arrays are already sorted, there exist three cases.

1. $x_{i-1} > y_{j}$ — array $x$ contains too much values, $i$ need to decrease
2. $y_{j-1} > x_{i}$ — array $x$ contains too few values, $i$ need to increase
3. $x_{i-1} \le y_{j}$ and $y_{j - 1} \le x_{i}$ — the expected case

In the third case, the separation can be compressed as

$$ \cdots, \min(x_{i-1}, y_{j-1}), \max(x_{i-1}, y_{j-1}) \quad | \quad \min(x_{i}, y_{j}), \max(x_{i}, y_{j}) \cdots $$

Then the median can be given as

$$ M = \left\{ \begin{array}{ll} \max(x_{i-1}, y_{j-1}) & m + n = 2t - 1, t \in \mathbb{N^*} \\ \\ \dfrac{\min(x_{i}, y_{j}) + \max(x_{i-1}, y_{j-1})}{2} & k = 2t, t \in \mathbb{N^*} \end{array} \right. $$

In order to find the correct separation, that is, the correct value of $i, j$, in $O(\log (m + n))$ time complexity, we use binary search algorithm.

Firstly, check the size of the two arrays. Let the shorter vector to be the first array, indexed by `i`, and the longer one to be the second array, indexed by `j`. Let the size of the first array to be `m`, and the size of the second array to be `n`.

Set the range of `i` with left reachable bound `left = 0` and right reachable bound `right = m`. 

We start a loop until the program get the final answer

- calculate `i = (left + right) / 2`, `j = (m + n + 1) / 2 - i ` (round down)
- Determine the four values `x[i-1]`, `x[i]`, `y[i-1]`, `y[i]`
    - `x[i-1] > y[j]`, expected `i` in the right region, `left = i + 1`
    - `y[j-1] > x[i]`, expected `i` in the left region, `right = i`
    - If not two cases above, then return the median based on the four values

### Extra discussion

It is much easier to perform this problem by merge-like algorithm, but it will take $O(\max(m, n))$ time complexity.

Since we need to determine the values $x_{i-1}, x_i, y_{j-1}, y_j$, but in that the range of $i, j$ is actually $0 \le i \le m,\ 0 \le j \le n$, some corner cases may need the value which is out of range.

For $i = 0$ or $j = 0$, we assign `INT_MIN` to $x_{i-1}$ or $y_{j - 1}$, then they will lose in all kinds of comparison. 

For $i = m$ or $j = n$, we assign `INT_MAX` to $x_i$ or $y_j$, then they will win in all kinds of comparison. 

The reason why we use the shorter array to be the first array is that, it can ensure the calculated index $j$ for the second array within the range. With $m \le n$, we can ensure that $0 \le j \le n$. Moreover, it can show that our algorithm in fact has $O(\log(\min(m, n)))$ time complexity.

## Solution

```c++
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        if (nums1.size() > nums2.size())
            return findMedianSortedArrays(nums2, nums1);
        int left = 0;
        int right = nums1.size();
        int i, j;
        int s = (nums1.size() + nums2.size() + 1) / 2;
        int flag = (nums1.size() + nums2.size()) % 2;
        int xsmall, xlarge, ysmall, ylarge;
        double res;
        while (true) {
            // Calculate partition position
            i = (left + right) / 2;
            j = s  - i;
            // Value assignment for four values
            xsmall = (i == 0) ? INT_MIN : nums1.at(i - 1);
            ysmall = (j == 0) ? INT_MIN : nums2.at(j - 1);
            xlarge = (i == nums1.size()) ? INT_MAX : nums1.at(i);
            ylarge = (j == nums2.size()) ? INT_MAX : nums2.at(j);
            // Too many values in array x
            if (xsmall > ylarge) {
                right = i;
                continue;
            }
            // Too few values in array x
            if (ysmall > xlarge) {
                left = i + 1;
                continue;
            }
            // Correct partition --- return values
            if (flag) {
                res = double(max(xsmall, ysmall));
            } else {
                res = (max(xsmall, ysmall) + min(xlarge, ylarge)) / 2.0;
            }
            return res;
        }
    }
};
```

