---
layout: post
title: More LeetCode Problems Banged out
---

Day Two of just banging some of these out.

-----------------------------------------------

First Problem [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/).

This one is taking me a second to find the non-brute force method.  I think this is just one of those sliding window problems, iterate over the array fiddling with some pointers based on the values of the numbers to find a maximum.

The more I think about this problem, the more I think it is similar to [Count Substrings with Dominant Ones](https://marshallgallatin.github.io/CountNumberOfSubstringsWithDominantOnes/).  In that case I was looking for substrings where the substring fit some formula, in this case I'm looking for the substrings where the substring formula is the sum of the numbers (and I just have to find the highest one instead of the count).

Hrrrmmm.... Unfortunately, my solution for that one was O(N^3) which is bad, there must be some trick.

I might do some research into this one.  Seems like once I learn the trick it would be easy to do both.  I don't like looking up the answers, but I am using these to learn and refresh my memory, and that may be the most expidient way of learning about this one.

What I think I know about this problem:
1. I think that the max sum subarrays will typically start and end with a positive number.  It could be the same number, or different numbers.
    * UNLESS the values are all negative, then the max sum is just the negative number closest to zero.
2. Of the subarrays with positive endpoints, the max sum subarray will have the smallest magnitute of negative values contained inside of it.

I'm not sure, its tricky.

Okay. So in the end I looked this one up, and the best solution is famous enough it has a name: [Kadane's Algorithm](https://en.wikipedia.org/wiki/Maximum_subarray_problem#Kadane's_algorithm)

I wasn't too far off on my first guess, I think I did a prefix multiply problem yesterday and essentially this is like a prefix sum problem.

I might've eventually gotten there, but now that I've seen the solution, I don't really feel the need to submit it.  I can't get [the implementation I saw](https://www.geeksforgeeks.org/largest-sum-contiguous-subarray/#expected-approach-using-kadanes-algorithm-on-time-and-o1-space) that explained it out of my head and anything I write now will be close to that (and why I try my best not to look the answer up).

EDIT: I did go back after a while and try to implement it just so I could make sure I remembered it, and here it is:
```c++
#include <limits>
#include <stddef.h>

class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int maxSum = nums[0];
        int result = maxSum;

        for(int i = 1; i < nums.size(); ++i)
        {
            maxSum = std::max(maxSum + nums[i], nums[i]);
            result = std::max(result, maxSum);
        }

        return result;
    }
};
```

Onto the next problem.

-----------------------------------------------

The next problem in the list I'm going down is the [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/).  Which oh, geez, I think its going to be practically the same answer.

I'm going to implement this one, just so I know whether or not I've actually understood the first solution and to see whether or not it is similar.

While it might be similar, its definitely not the same solution.  It makes sense, two negatives make a positive in multiplication.

This may need a slightly different approach....

Here is the first solution I found:
```c++
#include <limits>
#include <algorithm>

class Solution {
public:
    int maxProduct(vector<int>& nums) {       
        int maxProduct = 1;
        int result = std::numeric_limits<int>::min();

        for(int i = 0; i < nums.size(); ++i)
        {
            maxProduct = maxProduct * nums[i];
            result = std::max(maxProduct, result);
            maxProduct = maxProduct == 0 ? 1 : maxProduct;
        }

        maxProduct = 1;
        for(int i = nums.size(); i > 0; )
        {
            --i;
            maxProduct = maxProduct * nums[i];
            result = std::max(maxProduct, result);
            maxProduct = maxProduct == 0 ? 1 : maxProduct;
        }

        return result;
    }
};
```

Its okay, but I was looking at solutions and there is a more optimized version that only loops once:

```c++
#include <limits>
#include <algorithm>

class Solution {
public:
    int maxProduct(vector<int>& nums) {       
        int maxProduct = nums[0];
        int minProduct = nums[0];
        int result = nums[0];

        for(int i = 1; i < nums.size(); ++i)
        {
            if(nums[i] < 0)
            {
                std::swap(maxProduct, minProduct);
            }
            maxProduct = std::max(maxProduct * nums[i], nums[i]);
            minProduct = std::min(minProduct * nums[i], nums[i]);
            result = std::max(maxProduct, result);
        }
        
        return result;
    }
};
```

Note, I didn't come up with this, I was reading the solutions and after reading [this solution](https://leetcode.com/problems/maximum-product-subarray/solutions/5785386/kaden-s-optimal-o-n-solution-beats-everything), I'm like DOH! it makes so much sense.