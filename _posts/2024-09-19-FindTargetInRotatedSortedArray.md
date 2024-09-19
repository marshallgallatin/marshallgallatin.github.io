---
layout: post
title: Search in Rotated Sorted Array
date: 2024-09-19 12:12:00 -06:00
---

The next problem I decided to do was [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/).  I think my solution is pretty unique, and probably not the most optimized, but it should be 0(log n) complexity.  Since I just did [Find Mimimum in Rotated Sorted Array](/MinimumInRotatedSortedArray/), I immediately thought hey, if I can find the minimum, then I just have to search either the left or right of that to find it, so thats what I did:

```C++
class Solution {
public:

    int findMinIndex(vector<int>& nums) {
        // trivial case, if the number at the end of the array is > the number at the beginning, then the array was rotated nums.size() times and the first number is the minimum.
        const int& beginningNumber = nums[0];
        const int& endNumber = nums[nums.size() - 1];
        if(endNumber > beginningNumber)
        {
            return 0;
        }

        int midpointIndex = nums.size() / 2;
        int endIndex = nums.size() - 1;
        int beginIndex = 0;
        int midpointValue = nums[midpointIndex];

        int minValue = endNumber;
        int minIndex = endIndex;

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
                minIndex = midpointIndex;
                // The minimum value is somewhere to the left of the midpointIndex (or IS the midpointIndex)
                endIndex = midpointIndex;
                midpointIndex = beginIndex + ((midpointIndex - beginIndex) / 2);
            }

            midpointValue = nums[midpointIndex];
        }

        return minIndex;
    }

    int search(vector<int>& nums, int target) {
        // find the minimumIndex, which tells us where the inflection point in the array is.  Given that point, we can search a subset for the target.
        int minIndex = findMinIndex(nums);
        
        int beginIndex = 0;
        int endIndex = nums.size() - 1;
        int midpointIndex = endIndex / 2;

        // just check that the bounds are the right value
        if(nums[minIndex] == target)
        {
            // found it, return the value
            return minIndex;
        }
        else if(nums[endIndex] == target)
        {
            return endIndex;
        }
        else if(nums[beginIndex] == target)
        {
            return beginIndex;
        }

        // Check to see if the target is > the end, if it is, we search to the left of the min index, if its not we search to the right
        if(target > nums[endIndex])
        {
            endIndex = minIndex;
            midpointIndex = endIndex / 2;
        }
        else
        {
            beginIndex = minIndex;
            midpointIndex = beginIndex + ((endIndex - beginIndex)/2);
        }
        


        int midpointValue = nums[midpointIndex];
        while(midpointIndex != beginIndex && midpointIndex != endIndex)
        {
            if(midpointValue == target)
            {
                // found it, return the value
                return midpointIndex;
            }

            if(midpointValue > target)
            {        
                // The target is to the left of the midpoint
                // move the midpointIndex halfway to the beginning
                endIndex = midpointIndex;
                midpointIndex = beginIndex + ((midpointIndex - beginIndex) / 2);

            }
            else
            {
                // The target is to the right of the midpoint
                // move the midpointIndex halfway to the end
                beginIndex = midpointIndex;
                midpointIndex = midpointIndex + ((endIndex - midpointIndex) / 2);
            }

            midpointValue = nums[midpointIndex];
        }

        // not found
        return -1;

    }
};
```

First I find the minValue in the array which is O(log n), then I search left or right of that for the target which is O(log n).  So my solution is O(2 * log n) which boils down to O(log n).  So while I'm in the ballpark complexity wise, I recognize there is probably a better solution to find it.