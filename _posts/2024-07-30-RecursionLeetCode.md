---
layout: post
title: LeetCode Recursive Problem
date: 2024-07-30 14:59:39 -06:00
---

I went ahead and did a second LeetCode problem today, specifically the [Add Two Numbers](https://leetcode.com/problems/add-two-numbers/) problem.  Immediately this sounded like a recursion problem, and it has been a minute since I did a recursive problem like this, so figured I'd take a stab at it.  Here is my first submission:

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */

ListNode* addRecursive(ListNode* left, ListNode* right, int carryBit)
{
    if (left == nullptr && right == nullptr)
    {
        if(carryBit > 0)
        {
            return new ListNode(carryBit);
        }
        else
        {
            return nullptr;
        }
    }
    else
    {
        const int leftVal = (left == nullptr) ? 0 : left->val;
        const int rightVal = (right == nullptr) ? 0 : right->val;
        const int newVal = leftVal + rightVal + carryBit;
        const int digit = newVal % 10;
        const int newCarryBit = newVal > 9 ? 1 : 0;
        return new ListNode
        (
            digit, 
            addRecursive
            (
                (left == nullptr) ? nullptr : left->next, 
                (right == nullptr) ? nullptr : right->next, 
                newCarryBit
            )
        );
    }
}

class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        return addRecursive(l1, l2, 0);
    }
};
```

It is really a pretty basic sort of problem, essentially you're just doing simple basic first grade math on a list of digits.

It was accepted, and given my recent experience where LeetCode times would vary, I'm not going to try and over optimize this one.  But yes, I can still write a recursive function!