---
layout: post
title: Banging out a few problems
date: 2024-09-17 14:47:00 -06:00
---

I'm working on a few different problems right now, figured I'd just make one post and post a few solutions here.

-----------------------------------------------

First problem, [Best time to buy and sell a stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/).  I didn't realize this was "Easy" and it was, took only a few minutes to figure out.
```c++
#include <limits>

class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int minPrice = std::numeric_limits<int>::max();
        int maxProfit = 0;

        for(const auto price : prices)
        {
            if(price < minPrice)
            {
                minPrice = price;
            }
            else
            {
                if((price - minPrice) > maxProfit)
                {
                    maxProfit = price - minPrice;
                }
            }
        }
        return maxProfit;
    }
};
```

Its a pretty simple loop over the list, see if the number will increase the max profit or not.

-----------------------------------------------

Second problem, [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/).

Here is a trivial solution
```c++
#include <set>

class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        std::set<int> numsInArray;
        for(const auto num : nums)
        {
            if(numsInArray.contains(num))
            {
                return true;
            }
            numsInArray.insert(num);
        }
        return false;
    }
};
```

I'm honestly curious if there is a simpler way.  You have to look at each element, you have to track which numbers you've seen.  I saw another solution in the discussion which was essentially `return (len(nums) != len(set(nums)))` (looks like a python solution).  The one problem I see with this is that you always have to create the full set of all the numbers, so if there are 10^9 unique numbers except the first two numbers are 1,1 then you end up creating a set with 10^9 - 1 numbers in it, eating up alot of unneeded memory and time to create the set.  Whereas my solution above will create a very small set with 1 item in it, the second step will see that the number is already in the set and quit then.  So my set with the same input would only contain 1 item.

I'm pretty sure the above is close to optimal, I did try swapping the set for unordered_set, and maybe it was slightly faster, but hard to tell with LeetCode.

-----------------------------------------------

Third Problem, [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self).

This is the first really interesting one in this list... and I guess that is why its the first medium one.

There are a couple of restrictions that make this more difficult...
> You must write an algorithm that runs in O(n) time and without using the division operation.

Here is what I came up with... its technically O(n) (more like O(3N), but who's counting), and does not do division:
```c++
#include <stddef.h>

class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int runningPrefixProduct = 1;
        int runningSuffixProduct = 1;

        vector<int> prefixProducts(nums.size(), 1);
        vector<int> suffixProducts(nums.size(), 1);

        for(size_t index = 0; index < nums.size(); ++index)
        {
            prefixProducts[index] = runningPrefixProduct;
            runningPrefixProduct *= nums[index];
        }

        for(size_t rindex = nums.size(); rindex > 0; )
        {
            --rindex;
            suffixProducts[rindex] = runningSuffixProduct;
            runningSuffixProduct *= nums[rindex];
        }

        std::vector<int> outputArray(nums.size(), 0);
        for(size_t index = 0; index < nums.size(); ++index)
        {
            outputArray[index] = prefixProducts[index] * suffixProducts[index];
        }

        return outputArray;
    }
};
```

What it does is precompute the multiplication before (prefix) and after (suffix) the index with two different loops, one starting at the beginning and one stopping at the end.  Then it creates the output array by multiplying them together.

The problem description has a followup...

>Follow up: Can you solve the problem in O(1) extra space complexity? (The output array does not count as extra space for space complexity analysis.)

Mine is not O(1) for "extra space complexity".  I'm trying to think how this is possible, I can see getting rid of the "suffixProducts" and compute that on the fly, but I still don't see how you get rid of at least one array of values.... let me see what I can come up with.

Here it is without the prefix/suffix vectors, but I still have a single runningProducts vector that is the output when finished:

```c++
#include <stddef.h>

class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int runningPrefixProduct = 1;
        int runningSuffixProduct = 1;

        vector<int> runningProducts(nums.size(), 1);

        for(size_t index = 0; index < nums.size(); ++index)
        {
            runningProducts[index] = runningPrefixProduct;
            runningPrefixProduct *= nums[index];
        }

        for(size_t rindex = nums.size(); rindex > 0; )
        {
            --rindex;
            runningProducts[rindex] = runningProducts[rindex] * runningSuffixProduct;
            runningSuffixProduct *= nums[rindex];
        }

        return runningProducts;
    }
};
```

This might satisfy the intent of the followup.  It says the output array does not count as extra space, so I could rename `runningProducts` to `outputArray` and that would fit the followup.  As far as I'm concerned, I'm counting this.  Side effect is one less loop through the array, so a smaller O(n) than before.