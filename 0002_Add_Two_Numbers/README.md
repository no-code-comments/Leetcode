# Add Two Numbers

[Medium] — https://leetcode.com/problems/add-two-numbers/

## Problem

### Description

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order**, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

### Examples

```
Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [7,0,8]
Explanation: 342 + 465 = 807.
```

```
Input: l1 = [0], l2 = [0]
Output: [0]
```

```
Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
Output: [8,9,9,9,0,0,0,1]
```

### Constraints

- The number of nodes in each linked list is in the range `[1, 100]`.
- `0 <= Node.val <= 9`
- It is guaranteed that the list represents a number that does not have leading zeros.

## Procedure

### Basic algorithm

The algorithm is just like using a vertical calculation to calculate the sum digit by digit.

Iterate the number from beginning to end, read the digits in two list (`0` if `nullptr`)

- add digits from two list with the carry digit
- put the sum into the new list

At the end, we need to check the carry digit again.

### Extra discussion

Since the sum digit will be calculated in the loop, so we don’t know the first digit when initialization. There are two solution

- Using a `sentinel`, occupying the first item. Then we return the true head `sentinel->next`. In this case, we should not use `new` operation for `sentinel` since we don’t need it out of the function. (Used in our solution)
- Calculating the sum digit and carry digit first and initialize the `head` of the digits.

## Solution

```c++
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode sentinel = ListNode();
        ListNode *current = &sentinel;
        int p;
        int q;
        int c = 0;
        int sum;
        while (l1 != nullptr || l2 != nullptr) {
            l1 != nullptr ? p = l1->val : p = 0;
            l2 != nullptr ? q = l2->val : q = 0;
            sum = p + q + c;
            c = sum / 10;
            current->next = new ListNode(sum % 10);
            current = current->next;
            if (l1 != nullptr) l1 = l1->next;
            if (l2 != nullptr) l2 = l2->next;
        }
        if (c > 0) current->next = new ListNode(c);
        return sentinel.next;
    }
};
```

