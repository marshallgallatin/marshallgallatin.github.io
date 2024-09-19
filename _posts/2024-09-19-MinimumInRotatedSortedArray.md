---
layout: post
title: Find Minimum in Rotated Sorted Array
---

The next problem I looked at was the [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array) problem.

This one only took me about 30 minutes, maybe less, it wasn't too hard:
```c++
#include <limits>

class Solution {
public:
    int findMin(vector<int>& nums) {
        // trivial case, if the number at the end of the array is > the number at the beginning, then the array was rotated nums.size() times and the first number is the minimum.
        const int& beginningNumber = nums[0];
        const int& endNumber = nums[nums.size() - 1];
        if(endNumber > beginningNumber)
        {
            return beginningNumber;
        }

        int minValue = endNumber;
        int midpointIndex = nums.size() / 2;
        int endIndex = nums.size() - 1;
        int beginIndex = 0;
        int midpointValue = nums[midpointIndex];

        // [ 4, 5, 6, 7, 0, 1, 2]
        //   B        M        E
        //   0        3        6

        // first iteration:
        // minV = 2, mpv == 7  (7 > 2)
        // [ 4, 5, 6, 7, 0, 1, 2]
        //            B  M     E
        //            3  4     6

        // second iteration:
        // minV = 2, mpv == 0  (0 <= 2)
        // set minV = 0;
        // [ 4, 5, 6, 7, 0, 1, 2]
        //            BM  E     
        //            3   4

        // minV = 0, mpv == 7  (7 > 0)
        // [ 4, 5, 6, 7, 0, 1, 2]
        //            BM  E     
        //            3   4
        while(midpointIndex != beginIndex && midpointIndex != endIndex)
        {
            if(midpointValue > minValue)
            {
                // The midpointIndex is to the left of the inflection point, minimum is between the midpoint and the end
                // move the midpointIndex halfway to the end
                beginIndex = midpointIndex;
                midpointIndex = midpointIndex + ((endIndex - midpointIndex) / 2);
            }
            else
            {
                // We have a new min value
                minValue = midpointValue;
                // The minimum value is somewhere to the left of the midpointIndex (or IS the midpointIndex)
                endIndex = midpointIndex;
                midpointIndex = beginIndex + ((midpointIndex - beginIndex) / 2);
            }

            midpointValue = nums[midpointIndex];
        }

        return minValue;


    }
};
```

I've left an example in the comments that I used to figure out the way to move the iterators in the data.  Essentially it's just a binary search where you check if the midpoint is more or less than the minimum value we've seen so far.  We know that if its greater, then we're too far to the left and need to work to the right, and if its less, than we know that the minimum is somewhere to the right.

And also, we can trivially check if the first value is less than the last value, then we know that its just a sorted array and the min value is the first value. (Think about it some, it makes sense).