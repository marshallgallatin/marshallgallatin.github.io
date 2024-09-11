---
layout: post
title: Count Substrings with Dominant Ones
---

I'm working on doing more leetcode problems quicker. This time I'm looking [at this problem](https://leetcode.com/problems/count-the-number-of-substrings-with-dominant-ones/)

Here's my solution:
```c++
#include <vector>
#include <stdint.h>
#include <iostream>

class Solution {
private:
    struct CountData
    {
        int numZeros;
        int numOnes;
    };

public:
    int numberOfSubstrings(string s) {
        // general idea, iterate over the string
        // how to store the info we need.... what info do we need
        // for each substring, need to know how many zeros and how many ones
        // 2D array on the substring index?  Data is struct with numZeros numOnes?

        const auto stringLength = s.size();

        std::vector<std::vector<CountData>> substringCountData(stringLength, std::vector<CountData>(stringLength, {0,0}));

        // x,y  Let L = 8;
        // 0,i -> 0,L
        // 
        // i,i -> i,L
        // x = 0 to i
        // y = i to L
        // for index 0
        // 0,0 -> 0,8
        // for index 1
        // 0,1 -> 0,8
        // 1,1 -> 1,8

        // start is x
        // stop is y

        // iterate over the string
        for(size_t stringIndex = 0; stringIndex < stringLength; ++stringIndex)
        {
            bool isZero = s[stringIndex] == '0';

            // iterate over x
            for (size_t x = 0; x <= stringIndex; ++x)
            {
                // iterate over y
                for (size_t y = stringIndex; y < stringLength; ++y)
                {
                    CountData& theCountData = substringCountData[x][y];
                    if(isZero)
                    {
                        ++(theCountData.numZeros);
                    }
                    else
                    {
                        ++(theCountData.numOnes);
                    }
                }
            }
        }

        int countOfDominantSubstrings = 0;
        for (int i = 0; i < stringLength; ++i)
        {
            for(int j = i; j < stringLength; ++j)
            {
                const CountData& theCountData = substringCountData[i][j];
                if(theCountData.numOnes >= (theCountData.numZeros * theCountData.numZeros))
                {
                    ++countOfDominantSubstrings;
                }
            }
        }

        return countOfDominantSubstrings;
    }
};
```

I left my comments in about how I reasoned to solve it.  I also had a pen and paper out and eventually understood how to traverse the 2D array to increment the correct locations.

Essentially, the 2D array is an array that contains how many zeros and ones are in a given substring where the indicies of the array are the first and last character index in the array.

You loop over the string once character by character, and update all the locations that include that index.  I think it might be possible to simplify this some more.... I don't think we have to actually store both numZeros and numOnes since its either zero or one.

## Time Limit Exceeded

Interestingly I got a Time Limit Exceeded error when trying to submit this solution.  I wonder if this is the seemingly random nature of the performance on leetcode.

I added that test case to the test cases and my code does correctly work on that case, just the overall performance must not be good enough (or their servers are randomly bogged down again).

After running it 3 or 4 times, I continued to get this error.  I am going to try my simplification that I mentioned above.

And while removing the "numZeros" count, I can just "do nothing" if its a zero, which should reduce the runtime by quite a bit.

Heres my more optimized attempt:
```c++
#include <vector>
#include <stdint.h>
#include <iostream>

class Solution {
public:
    int numberOfSubstrings(string s) {
        // general idea, iterate over the string
        // how to store the info we need.... what info do we need
        // for each substring, need to know how many zeros and how many ones
        // 2D array on the substring index?  Data is struct with numZeros numOnes?

        const auto stringLength = s.size();

        std::vector<std::vector<int>> substringOnesCount(stringLength, std::vector<int>(stringLength, 0));

        // x,y  Let L = 8;
        // 0,i -> 0,L
        // 
        // i,i -> i,L
        // x = 0 to i
        // y = i to L
        // for index 0
        // 0,0 -> 0,8
        // for index 1
        // 0,1 -> 0,8
        // 1,1 -> 1,8

        // start is x
        // stop is y

        // iterate over the string
        for(size_t stringIndex = 0; stringIndex < stringLength; ++stringIndex)
        {
            bool isOne = s[stringIndex] == '1';
            if(isOne)
            {
                // iterate over x
                for (size_t x = 0; x <= stringIndex; ++x)
                {
                    // iterate over y
                    for (size_t y = stringIndex; y < stringLength; ++y)
                    {
                        ++substringOnesCount[x][y];
                    }
                }
            }
        }

        int countOfDominantSubstrings = 0;
        for (int i = 0; i < stringLength; ++i)
        {
            for(int j = i; j < stringLength; ++j)
            {
                const int& numOnes = substringOnesCount[i][j];
                // j - i gives us length - 1 because its zero indexed, need to +1 to get the substring length
                const int numZeros = ((j - i) + 1) - numOnes;
                if(numOnes >= (numZeros * numZeros))
                {
                    //std::cout << "Dominant i,j: " << i << "," << j << std::endl;
                    ++countOfDominantSubstrings;
                }
            }
        }

        return countOfDominantSubstrings;
    }
};
```

and unfortunately, this **STILL** exceeds the time limit

I'm not seeing a much more efficient way of doing this... I wonder if I didn't use a vector and instead used an array maybe? I don't think the added complexity of this is worth it, and I'm not confident this will make any difference (we pass the size parameters in at vector construction, so there shouldn't be any copying/reallocation at any point, so the underlying data is probably very similar).

... also apparently, many many people have run into this timeout problem in the discussion section.

I could avoid just a little bit of math at the end where I check for dominance for the i == j index, I don't actually have to do any math, its pretty much if its a 1 its dominant and if its a 0 it isn't.  I extrapolated this for a few substring sizes, it works, but it still times out (it gets futher than the code above though, so there was an improvement):

```c++
#include <vector>
#include <stdint.h>
#include <iostream>

class Solution {
public:
    int numberOfSubstrings(string s) {
        // general idea, iterate over the string
        // how to store the info we need.... what info do we need
        // for each substring, need to know how many zeros and how many ones
        // 2D array on the substring index?  Data is struct with numZeros numOnes?

        const auto stringLength = s.size();

        std::vector<std::vector<int>> substringOnesCount(stringLength, std::vector<int>(stringLength, 0));
        

        // x,y  Let L = 8;
        // 0,i -> 0,L
        // 
        // i,i -> i,L
        // x = 0 to i
        // y = i to L
        // for index 0
        // 0,0 -> 0,8
        // for index 1
        // 0,1 -> 0,8
        // 1,1 -> 1,8

        // start is x
        // stop is y

        // iterate over the string
        for(size_t stringIndex = 0; stringIndex < stringLength; ++stringIndex)
        {
            bool isOne = s[stringIndex] == '1';
            if(isOne)
            {
                // iterate over x
                for (size_t x = 0; x <= stringIndex; ++x)
                {
                    // iterate over y
                    for (size_t y = stringIndex; y < stringLength; ++y)
                    {
                        ++substringOnesCount[x][y];
                    }
                }
            }
        }

        int countOfDominantSubstrings = 0;
        for (int i = 0; i < stringLength; ++i)
        {
            for(int j = i; j < stringLength; ++j)
            {
                // j - i gives us length - 1 because its zero indexed, need to +1 to get the substring length
                const int substringSize = ((j - i) + 1);
                const int& numOnes = substringOnesCount[i][j];
                // optimization, if the substring is only 1 character long, we don't have to actually do the math on whether its dominant, it is if it has a 1
                // if its two characters long, and has at least 1 one, then its also dominant and we don't have to do any math on it because 1 >= 1*1 if there is a single one in the substring
                switch(substringSize)
                {
                    case 1:
                    case 2:
                        if(numOnes)
                        {
                            ++countOfDominantSubstrings;
                        }
                        break;
                    case 3:
                        if(numOnes >= 2)
                        {
                            ++countOfDominantSubstrings;
                        }
                        break;
                    case 4:
                        if(numOnes >= 3)
                        {
                            ++countOfDominantSubstrings;
                        }
                        break;
                    default:
                        const int numZeros = substringSize - numOnes;
                        if(numOnes >= (numZeros * numZeros))
                        {
                            ++countOfDominantSubstrings;
                        }
                        break;
                }
            }
        }

        return countOfDominantSubstrings;
    }
};
```

For the record, the case that exceeds the time limit is this case:
```c++
/*
"111101100001100001010010001110111001000110110011001010110001111110101101111011101000110010110000001111101000011000101111110100010101001110110111000010101101100010000111101000010001110011010100101010101000111110101100100010010111010101000111011010100000100011010101110010110011011000110010101100100001101110110101001110101000011110101011111000110100010011011110010001011000101111011111000001100101001100001000110110110101110001000001110000001100001100000101101010011010101110101000111010000010000001000101101101010001110100111101000011000110111101111111000001000101110100000011100111110111111100001100100110101000100000111101101010001000101110110010011111001000110000111001110001001111000010000011001100011010001000010100000011001100010010010111101011110110101110011111000111000011011100011110000111001001110110111100001000100110010000110010010001001100010110111110110000011111011110000010010011011100110111001010011011111010000001100011000001010010010101011101001010110101010101100101000010111111010111011111101100100111110000101111001011100001011110011111101010110110101100010111001100010110110010010001010011001111111110101010110001001100110110000101010010011100010101001111000111010110000111011110101110100101100101110010011101000101110011101010101110101011100001000011110111000000111101000001000100111000001110111100101111001010001010011100010111101100010001011111100101010010000100110001110011110000010101110111111101111111001011110000011100010101011101010110101001000010001110111110011100011100111100011001110001110011110010000010000111100101001010110010011100100101100010100010111100111010110110110100100010100101010101100110011001000111110101011010000001000101111011010100000101010100110101001001000100010001001100101011011011111111100010000010111101011011011101110101111011001110110110011111100111011110011100011101101111010000100111000001100111110101111011101011011000011001010111110100100110010110111111111110000100011010001010010001000100010001101100000101000010111111010010111110001111011101101011001000100010101010110011011101011011011111011011000100011011110111001000011000010011011011001001010000000100110110000111110111001010101011101010100110111000011101111001000000001000110110110101100110010111001011111010111100001001111011010010010101001010110010000010011000000000101110111101001110010111010101000011110111000110100010000111101111101101011101111011011101001010011111011111110110110000011110000011111011011100111110111011100001110111010010111010100010110010001010001111101001010100111101001010110010000011100100111110110001100010110000000101010111111011010001010100110110001110001010111100001001110000010010010110111011110011111100010100000011111100110110100110110101010100111101000000111110001111110010010001111010011101000010001000001010110000111111001101111111110101011001110110000011010011010011000011101010101101111111111000111010100010011010100011110011100011011100011111011101000011010001001101111110110001111100100111011110010100110010110000001101001011000100001000000101000101110011110010001100001101011101110010111001011001111100011100111010100100011100100001001001100111001010100011100010001001001101110110101001100111010010011010100110011110110001100110101011111101011011111101100101000110101100111011111000011111010101001111100101010001000000000001011100011111100100001100110101000000001010110010011011001011110111101011000001101001010001010011000100011011100101100100000111100101111011110010010111101001001011100000010111001011110000101000001000010000101011100111111101111101100000001111001111111011011001110011011101111111111100111101000010011110100110010100111111101110000100011110111011100110010110001"
*/
```

For the moment I'm going to put this problem on a shelf.  I do think there is a more optimal solution, I think mine is O(N^3) complexityh but I think there there is probably more optimized soution.  I'm wondering if there is a way where I could store a running total of the number of ones up until that point in the string somehow, and maybe be able to calculate the answer using that.  This isn't a trivial problem, I can see why it has such a low acceptance rate (~15%, the lowest acceptance rate of the medium problems on leetcode).