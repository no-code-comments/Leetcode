# Two Sum

[Easy] — https://leetcode.com/problems/two-sum/

## Problem

### Description

Given an array of integers `nums` and an integer `target`, return *indices of the two numbers such that they add up to `target`*.

You may assume that each input would have **exactly one solution**, and you may not use the *same* element twice.

You can return the answer in any order.

### Examples

```
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Output: Because nums[0] + nums[1] == 9, we return [0, 1].
```

```
Input: nums = [3,2,4], target = 6
Output: [1,2]
```

```
Input: nums = [3,3], target = 6
Output: [0,1]
```

### Constraints

- `2 <= nums.length <= 10^3`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`
- **Only one valid answer exists.**

## Procedure

### General Idea

The problem is about finding specific pair of values, and we can degrade it from finding pair to searching one element based on another element. In this problem, we need to meet the requirement of $a_i + a_j = t$ with number $a$ and $b$, then we transform the question into searching number $a_j = t - a_i$.

|               | $a_1$          | $a_2$          | $\cdots$ | $a_{n - 2}$    | $a_{n - 1}$    |
| ------------- | -------------- | -------------- | -------- | -------------- | -------------- |
| $t - a_1$     | $\circleddash$ |                |          |                |                |
| $t-a_2$       |                | $\circleddash$ |          |                |                |
| $\vdots$      |                |                | $\ddots$ |                |                |
| $t - a_{n-2}$ |                |                |          | $\circleddash$ |                |
| $t - a_{n-1}$ |                |                |          |                | $\circleddash$ |

This table shows the outline of the searching algorithm. We need to fill in all the blanks by searching the elements. The elements cannot be used repeatedly, so those corresponding blocks in the table are labelled as $\circleddash$. After performing one search, we are able to tell the result for each line, whether $t-a_i$ is equal to any element in $a_0, a_1, \cdots, a_{n-1}$. 

Moreover, since the pair relationship (sum in this problem) doesn’t have requirement on order, meaning that the result of $a_j$ when searching $t-a_i$ is the same as that of $a_i$ when search $t -a_{j}$, so we can reduce half of the comparison shown as the following table.

|               | $a_0$          | $a_1$          | $\cdots$ | $a_{n - 2}$    | $a_{n - 1}$    |
| ------------- | -------------- | -------------- | -------- | -------------- | -------------- |
| $t - a_0$     | $\circleddash$ | $\circleddash$ | $\cdots$ | $\circleddash$ | $\circleddash$ |
| $t-a_1$       |                | $\circleddash$ | $\cdots$ | $\circleddash$ | $\circleddash$ |
| $\vdots$      |                |                | $\ddots$ | $\vdots$       | $\vdots$       |
| $t - a_{n-2}$ |                |                |          | $\circleddash$ | $\circleddash$ |
| $t - a_{n-1}$ |                |                |          |                | $\circleddash$ |

According to this table, we observe that when searching $t - a_i$, we only need to perform the search operation in $a_0, a_1, \cdots, a_{i - 1}$, instead of the entire array.

### Basic Algorithm

Using a hash table `table` to store values in `nums` to search an element in $O(1)$ time.

Iterate `i` of `nums` from beginning to end, and find its complement `target - nums[i]`

- if we can find one element in `table`, then return the pair
- if we can’t find one, then insert `nums[i]` into `table` and continue

### Extra discussion

We need to be careful when the hash table inserts identical elements with different index, since the former item will be overwritten after inserting an item with the same key. However, in this problem, we don’t need to take it into consideration, because we only need two numbers with only 1 exact solution.

If the for loop ends without finding a pair, meaning that we don’t find any pair having a sum of the target value. The constraints ensure that this case will not happen.

## Solution

```c++
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> table;
        int num;
        for (unsigned int i = 0; i < nums.size(); ++i) {
            num = nums.at(i);
            auto pos_it = table.find(target - num);
            if (pos_it != table.end()) return vector<int>({pos_it->second, int(i)});
            table.insert({num, i});
        }
        throw -1;
    }
};
```

