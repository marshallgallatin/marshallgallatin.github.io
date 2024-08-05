---
layout: post
title: Palindrome
---

I had a job interview today.  They asked me to write a code snippet, it was a palindrome finder.  And they wanted me to write it in C.  I think I did pretty well given the on the spot nature of the request, I'm pretty sure it worked, at least in the basic case.  Since this was just written out in an editor and I was unable to run the function, then I decided to see if LeetCode had the problem and they had something very similar in the [Valid Palindrome Problem](https://leetcode.com/problems/valid-palindrome/).

For my solution, I just wanted to have two pointers traverse the string (one from the beginning, the other from the end), and compare the characters.  The LeetCode problem had an additional wrinkle of including non-alphanumeric characters which I was to ignore.  This is what I came up with:

```c
#include <ctype.h>

bool isPalindrome(const char* s) 
{
    if (s == NULL)
    {
        return false;
    }

    char* b = (char*)s;
    char* e = b + strlen(s) - sizeof(char);
    while(b <= e)
    {
        if(!isalnum(*b))
        {
            ++b;
            continue;
        }
        else if(!isalnum(*e))
        {
            --e;
            continue;
        }
        char bChar = tolower(*b);
        char eChar = tolower(*e);

        if (bChar != eChar)
        {
            return false;
        }
        ++b;
        --e;
    }
    return true;
}
```

It worked.  So at least I'm happy my method would've worked.  I don't know how well the interview went, but at least this worked.