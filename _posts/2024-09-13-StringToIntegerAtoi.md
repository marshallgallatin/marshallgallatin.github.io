---
layout: post
title: String to Integer (atoi)
---

This problem was listed as medium: [String to Integer(atoi)](https://leetcode.com/problems/string-to-integer-atoi), so I thought I'd give it a shot.  Didn't think it would be too hard, and it wasn't.  Here is my solution:

```c++
#include <vector>
#include <ctype.h> 
#include <stdint.h>
#include <numeric>
#include <cassert>
#include <math.h>

int convertCharToDigit(const char& c)
{
    switch(c)
    {
        case '0':
            return 0;
        case '1':
            return 1;
        case '2':
            return 2;
        case '3':
            return 3;
        case '4':
            return 4;
        case '5':
            return 5;
        case '6':
            return 6;
        case '7':
            return 7;
        case '8':
            return 8;
        case '9':
            return 9;
    }
    assert(false && "This shouldn't happen, you've messed up the char to digit conversion");
    return -1;
}

class Solution {
public:
    int myAtoi(string s) {
        int returnVal = 0;

        bool negative = false;
        bool leadingWhitespace = true;
        bool done = false;

        std::vector<char> numbers;
        numbers.reserve(s.length());

        size_t charIndex = 0;

        // Handle leading whitespace:
        while(charIndex < s.length() && s[charIndex] == ' ')
        {
            ++charIndex;
        }

        if(charIndex == s.length())
        {
            // it was all whitespace, return 0
            return returnVal;
        }

        //check next char for sign
        if(s[charIndex] == '-' || s[charIndex] == '+')
        {
            if(s[charIndex] == '-')
                negative = true;
            ++charIndex;
        }

        if(charIndex == s.length())
        {
            // just a pos/negative sign with no numbers, return 0
            return returnVal;
        }

        while(charIndex < s.length() && isdigit(s[charIndex]))
        {
            // store the numbers
            numbers.push_back(s[charIndex]);
            ++charIndex;
        }

        // Process the numbers

        int rank = numbers.size() - 1;

        // TODO if rank is too big, the number won't even fit in a 64bit integer, 
        // but we have to look to see if there are leading zeros
        size_t numberIndex = 0;
        while(numberIndex < numbers.size() && numbers[numberIndex] == '0')
        {
            ++numberIndex;
            --rank;
        }

        // A 64bit int can handle an 18 digit number, but if we're at over 18 digits 
        // we know we have to round, so bail now.
        if(rank >= 18)
        {
            if(negative)
            {
                return std::numeric_limits<int>::min();
            }
            return std::numeric_limits<int>::max();
        }

        int64_t runningNumber = 0;
        while(numberIndex < numbers.size())
        {
            runningNumber += (convertCharToDigit(numbers[numberIndex]) * std::pow(10,rank));
            ++numberIndex;
            --rank;
        }

        if(negative)
        {
            runningNumber = -runningNumber;
        }

        if(runningNumber > std::numeric_limits<int>::max())
        {
            return std::numeric_limits<int>::max();
        }
        else if(runningNumber < std::numeric_limits<int>::min())
        {
            return std::numeric_limits<int>::min();
        }

        return static_cast<int>(runningNumber);

    }
};
```

This solution ran at 0ms beating 100% for runtime, but at 9.53 MB of memory, it only beats 10.57% for memory usage.

I suspect that I can get rid of the numbers vector, and update this to just use the passed in string...

```c++
#include <ctype.h> 
#include <stdint.h>
#include <numeric>
#include <cassert>
#include <math.h>

int convertCharToDigit(const char& c)
{
    switch(c)
    {
        case '0':
            return 0;
        case '1':
            return 1;
        case '2':
            return 2;
        case '3':
            return 3;
        case '4':
            return 4;
        case '5':
            return 5;
        case '6':
            return 6;
        case '7':
            return 7;
        case '8':
            return 8;
        case '9':
            return 9;
    }
    assert(false && "This shouldn't happen, you've messed up the char to digit conversion");
    return -1;
}

class Solution {
public:
    int myAtoi(string s) {
        const size_t sLength = s.length();
        int returnVal = 0;

        bool negative = false;

        size_t charIndex = 0;

        // Handle leading whitespace:
        while(charIndex < sLength && s[charIndex] == ' ')
        {
            ++charIndex;
        }

        if(charIndex == sLength)
        {
            // it was all whitespace, return 0
            return returnVal;
        }

        //check next char for sign
        if(s[charIndex] == '-' || s[charIndex] == '+')
        {
            if(s[charIndex] == '-')
                negative = true;
            ++charIndex;
        }

        if(charIndex == sLength)
        {
            // just a pos/negative sign with no numbers, return 0
            return returnVal;
        }

        // find first and last digit indicies
        size_t digitsBegin = charIndex;
        while(charIndex < sLength && isdigit(s[charIndex]))
        {
            ++charIndex;
        }
        size_t digitsEnd = charIndex;

        // Process the numbers
        int rank = (digitsEnd - digitsBegin) - 1;

        size_t numberIndex = digitsBegin;
        while(numberIndex < digitsEnd && s[numberIndex] == '0')
        {
            ++numberIndex;
            --rank;
        }

        // A 64bit int can handle an 18 digit number, but if we're at over 10 digits we 
        // know its getting rounded because int32 can't handle that, so bail now.
        if(rank >= 10)
        {
            if(negative)
            {
                return std::numeric_limits<int>::min();
            }
            return std::numeric_limits<int>::max();
        }

        int64_t runningNumber = 0;
        while(numberIndex < digitsEnd)
        {
            runningNumber += (convertCharToDigit(s[numberIndex]) * std::pow(10,rank));
            ++numberIndex;
            --rank;
        }

        if(negative)
        {
            runningNumber = -runningNumber;
        }

        if(runningNumber > std::numeric_limits<int>::max())
        {
            return std::numeric_limits<int>::max();
        }
        else if(runningNumber < std::numeric_limits<int>::min())
        {
            return std::numeric_limits<int>::min();
        }

        return static_cast<int>(runningNumber);

    }
};
```

This does reduce the memory usage to 9.29MB that beats 20% of submissions.

I'm not seeing anywhere where I'm allocating much memory at all, I'm curious as to where all the memory is getting consumed.  I look through the string, ignore any leading whitespace, handle a + or - character, store if its positive or negative, find all the digits in order... I really don't see where I'm allocating memory more than a few integers and booleans.

I am curious how leetcode classifies the results, is it by language? I would hope so, I would imagine C++ and Python could have very different runtimes and memory usage.
