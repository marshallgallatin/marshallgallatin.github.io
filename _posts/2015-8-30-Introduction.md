---
layout: post
title: Introduction
---

## Where to begin, where to begin.... ##

First off, the purpose of starting this blog is entirely to get 10 points of extra credit for my CS 371p class.  I feel like I should be upfront about this fact.  I guess I'm a little skeptical of the return on investment that this blog will have, but I am willing to try, maybe I'll discover that I'm wrong (which happens sometimes).

Secondly, my perspective on this class is going to be slightly different from many of the other students in the class.  I work full time at National Instruments, and I use C++ on an almost daily basis (among many other languages).  I'm worried that I'll slip into the "oh I know this already" mentality and not give this class the effort it deserves.   

We've only had two classes so far, theres not much to comment on there, but I find the 50 minute classes exceedingly short.  I guess I haven't had a short class in a while, it seems like we just get going good and class is up.  I've had Professor Downing before, both at UT a very, very, long time ago, and a class he taught at NI, so I think I know what to expect.  I enjoy it when professors treat the students like adults and not children - hand holding in college courses annoys me.  I find Downing's Socratic teaching style interesting, it keeps you engaged in anticipation of getting called on.

Even though I'm skeptical of the value of the blog, I do feel like I've already learned something.  I really like the simplicity of this blog's setup and hosting.  The idea of writing straight up Markdown and having it posted directly to the web is something I personally have played around with [before](http://www.cs.utexas.edu/users/mgallati/test.md).  The REALLY nice thing about this whole GitHub Pages/Jekyll method, is that I didn't have to configure anything, nor do I have to maintain a server, etc.  I would like to become more proficient with Markdown syntax, so this blog may be more beneficial than I let on.

One of the things that I always find interesting is the difficulty other students seem to have when setting up the build toolchain for a new class.  Maybe this is where experience does me a favor, I'm just always surprised that CS students can't figure out how to Google for the answer.  It takes all my strength not to post [lmgtfy](http://www.lmgtfy.com/?q=How+to+google) links in every thread on piazza.  The ability to research and find the answer yourself is a valuable skill in the workplace.

I'm really glad we're using Google Test, we use it at work and the framework is pretty awesome.  I can't wait to bust out the TEST_P tests.  As someone who works in the industry, I see what Downing is doing requiring the use of these build tools.  We use most of these kinds of tools at work every day, and while it may be overkill for a small object-oriented programming class, it is definitely going to be a valuable experience in the long run.  I'm looking forward to figuring out the TravisCI stuff when it comes up.

### Protip of the week ###

I've read a few of the other blogs that students have posted for this class, and this is inspired by a rant in a different [blog](http://lkolbly.github.io/cs371p-web/2015/08/30/first-post.html).

How to only include a header file once in C/C++:
There are two common methods, [include guards](https://en.wikipedia.org/wiki/Include_guard) and [pragma once](https://en.wikipedia.org/wiki/Pragma_once).  Include guards are common, and the older method for handling a header file being included more than once.  Pragma once is much cleaner and simpler, but it is less standard and only recently supported by some compilers.
#### Include guards ####
    #ifndef __NAME_OF_HEADER_FILE_H_
    #define __NAME_OF_HEADER_FILE_H_

    _code goes here_

    #endif

#### Pragma once ####

    #pragma once

    _code goes here_

As you can see, pragma once is much simpler and less error prone.

