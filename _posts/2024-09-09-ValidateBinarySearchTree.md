---
layout: post
title: Binary Search Tree
---

I've been a little slack doing these problems, but I started this one, and it took me a minute to figure it out: [https://leetcode.com/problems/validate-binary-search-tree/](https://leetcode.com/problems/validate-binary-search-tree/)

Here's my solution, its nothing crazy and I don't see a bunch of room for improvement:
```c++
#include <stdint.h>
#include <limits>
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    bool isValidBST(TreeNode* root, int64_t min, int64_t max)
    {
        // min < val < max
        const bool currentNodeIsValid = (min < root->val) && (root->val < max);

        return 
            currentNodeIsValid && 
            (root->left ? isValidBST(root->left, min, root->val) : true) &&
            (root->right ? isValidBST(root->right, root->val, max) : true);

    }

    bool isValidBST(TreeNode* root) {
        return isValidBST(root, std::numeric_limits<int64_t>::min(), std::numeric_limits<int64_t>::max());
    }
};
```

It took me a while to boil the problem down.  The naive solution is that the node's value is just between the left and the right, when in reality the node's value needs to be between some min and max based on the node's location in the tree.

It helped for me to write down the values on paper before coding the solution.
