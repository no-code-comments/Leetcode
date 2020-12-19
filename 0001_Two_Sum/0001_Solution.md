# Two Sum

[Easy] — https://leetcode.com/problems/two-sum/

## Problem

### Description

Given an array of integers `nums` and an integer `target`, return *indices of the two numbers such that they add up to `target`*.

You may assume that each input would have ***exactly\* one solution**, and you may not use the *same* element twice.

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

- `2 <= nums.length <= 103`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`
- **Only one valid answer exists.**

## Procedure

### Basic algorithm

Using a hash table to store values in `nums` to search an element in $O(1)$ time.

Iterate the number from beginning to end, and find its complement `target - num`

- if we can find one element in the previous hash table, then return the pair
- if we can’t find one, then insert the number and continue

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

