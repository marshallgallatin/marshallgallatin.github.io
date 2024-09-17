---
layout: post
title: Lowest Common Ancestor (modified)
---

So I'm doing a variation of the [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree) problem.  I was recently in a technical screening where I was given a variation of this problem where the nodes had parent pointers.  

The addition of the parent pointers makes the problem a bit easier I think.  Without parent pointers, the canonical solution is a recursive DFS search problem.  I understand why recursion exists (and I use it below for setting up the modified version), but I tend to lean iterative when there is a choice between iterative and recursive.

Just so you know, there is a bunch of setup code in here that does several things:
1. Creates a new type of node that is a TreeNode but also has a parent pointer
2. Copies the tree that was given, but uses the TreeNodeWithPointer data type instead (and properly sets the parent pointers)
3. Finds the p and q nodes in the new tree.

Here is the code:
```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */

#include <algorithm>
#include <vector>
#include <cassert>

struct TreeNodeWithParents : public TreeNode
{
    TreeNodeWithParents* parent;
    TreeNodeWithParents(int x) : TreeNode(x), parent(NULL) {}
    TreeNodeWithParents(int x, TreeNodeWithParents* parent) : TreeNode(x), parent(parent) {}
};

TreeNodeWithParents* createTreeWithParents(TreeNode* root, TreeNodeWithParents* parentsRoot)
{
    parentsRoot = new TreeNodeWithParents(root->val, parentsRoot);
    // Traverse the tree, create a new tree with parent pointers and return a pointer to the root of that tree.
    if(root->left != nullptr)
    {
        parentsRoot->left = createTreeWithParents(root->left, parentsRoot);
    }
    if(root->right != nullptr)
    {
        parentsRoot->right = createTreeWithParents(root->right, parentsRoot);
    }
    return parentsRoot;
}

TreeNodeWithParents* find(TreeNodeWithParents* root, TreeNode* node)
{
    if(root == NULL || node == NULL)
        return NULL;

    if(root->val == node->val)
    {
        return root;
    }
    
    if(root->left != NULL)
    {
        TreeNodeWithParents* leftVal = find(static_cast<TreeNodeWithParents*>(root->left), node);
        if(leftVal != NULL)
            return leftVal;
    }

    if(root->right != NULL)
    {
        TreeNodeWithParents* rightVal = find(static_cast<TreeNodeWithParents*>(root->right), node);
        if(rightVal != NULL)
            return rightVal;
    }

    return NULL;
}

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        
        TreeNodeWithParents* parentsRoot = createTreeWithParents(root, NULL);
        TreeNodeWithParents* parentsP = find(parentsRoot, p);
        TreeNodeWithParents* parentsQ = find(parentsRoot, q);

        assert(p->val == parentsP ->val);
        assert(q->val == parentsQ ->val);

        std::vector<TreeNode*> nodesIveSeen;

        TreeNodeWithParents* pPointer = parentsP;
        while(pPointer != NULL)
        {
            nodesIveSeen.push_back(pPointer);
            pPointer = pPointer->parent;
        }

        TreeNodeWithParents* qPointer = parentsQ;
        while(qPointer != NULL)
        {
            if(std::find(nodesIveSeen.begin(), nodesIveSeen.end(), qPointer) != nodesIveSeen.end())
            {
                return qPointer;
            }
            qPointer = qPointer->parent;
        }

        return NULL;
    }
};
```

This solution was accepted by LeetCode, so I know the method is correct at least for the cases they use for validation.  Basically I search up from the p node to the root, storing all the nodes I've seen previously.  Then I start at the q node and search up until I find a node I've previously seen before, and thats your least common ancestor.

I think this method is faster than always searching from the root, but its probably slower when you factor in having to create the binary tree with parent pointers.  Its also most likely more memory intensive than the recursive approach (but the recurisve approach is not free, the stack growing and shrinking while very cheap is not free).

One thing to note, theres a huge memory leak in here, because I don't ever delete the trees that were copied.  I'll be honest, its just a simple problem and when the process exits the memory would be freed.  I'm sure the code that LeetCode wraps around my code creates and deletes the trees correctly, but I don't really wanna bother with it.......

Bah, but my OCD has kicked in and now I want to delete the tree I created.

The reason I didn't want to do this is there is a lifetime problem with returning a pointer to the TreeNodeWithParents to the LeetCode framework.  I'm supposed to return just a TreeNode*, but a TreeNodeWithParents IS a TreeNode, so I was just returning that node instead of the one in the original.  So how do I return a pointer to the node if I delete it (use after free is a problematic bug).

But really, its not that hard.  My find function really is more generic and can operate on just straight up TreeNodes, so modify it so that I can find the node in the original TreeNode based tree and return that, then I'm free to um... free up the TreeNodeWithParents tree (well delete it).

Here is the updated code, added the "deleteTree" function to delete a parentsRoot based tree:

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */

#include <algorithm>
#include <vector>
#include <cassert>

struct TreeNodeWithParents : public TreeNode
{
    TreeNodeWithParents* parent;
    TreeNodeWithParents(int x) : TreeNode(x), parent(NULL) {}
    TreeNodeWithParents(int x, TreeNodeWithParents* parent) : TreeNode(x), parent(parent) {}
};

TreeNodeWithParents* createTreeWithParents(TreeNode* root, TreeNodeWithParents* parentsRoot)
{
    parentsRoot = new TreeNodeWithParents(root->val, parentsRoot);
    // Traverse the tree, create a new tree with parent pointers and return a pointer to the root of that tree.
    if(root->left != nullptr)
    {
        parentsRoot->left = createTreeWithParents(root->left, parentsRoot);
    }
    if(root->right != nullptr)
    {
        parentsRoot->right = createTreeWithParents(root->right, parentsRoot);
    }
    return parentsRoot;
}

void deleteTree(TreeNodeWithParents* parentsRoot)
{
    // Traverse the tree, create a new tree with parent pointers and return a pointer to the root of that tree.
    if(parentsRoot->left != nullptr)
    {
        deleteTree(static_cast<TreeNodeWithParents*>(parentsRoot->left));
    }
    if(parentsRoot->right != nullptr)
    {
        deleteTree(static_cast<TreeNodeWithParents*>(parentsRoot->right));
    }

    delete parentsRoot;
}

TreeNode* find(TreeNode* root, TreeNode* node)
{
    if(root == NULL || node == NULL)
        return NULL;

    if(root->val == node->val)
    {
        return root;
    }
    
    if(root->left != NULL)
    {
        TreeNode* leftVal = find(root->left, node);
        if(leftVal != NULL)
            return leftVal;
    }

    if(root->right != NULL)
    {
        TreeNode* rightVal = find(root->right, node);
        if(rightVal != NULL)
            return rightVal;
    }

    return NULL;
}

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        
        TreeNodeWithParents* parentsRoot = createTreeWithParents(root, NULL);
        TreeNodeWithParents* parentsP = static_cast<TreeNodeWithParents*>(find(parentsRoot, p));
        TreeNodeWithParents* parentsQ = static_cast<TreeNodeWithParents*>(find(parentsRoot, q));

        assert(p->val == parentsP ->val);
        assert(q->val == parentsQ ->val);

        std::vector<TreeNode*> nodesIveSeen;

        TreeNodeWithParents* pPointer = parentsP;
        while(pPointer != NULL)
        {
            nodesIveSeen.push_back(pPointer);
            pPointer = pPointer->parent;
        }

        TreeNodeWithParents* qPointer = parentsQ;
        while(qPointer != NULL)
        {
            if(std::find(nodesIveSeen.begin(), nodesIveSeen.end(), qPointer) != nodesIveSeen.end())
            {
                TreeNode* ptrToReturn = find(root, qPointer);
                deleteTree(parentsRoot);
                return ptrToReturn;
            }
            qPointer = qPointer->parent;
        }

        return NULL;
    }
};
```

Now I feel better.  I freed the memory just cycles before it all would've gotten freed up anyway 🤣