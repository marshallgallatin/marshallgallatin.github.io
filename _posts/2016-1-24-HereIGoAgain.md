---
layout: post
title: Here I Go Again
banner_video: '<iframe width="854" height="240" src="https://www.youtube.com/embed/sTd8XtZGK_8" frameborder="0" allowfullscreen></iframe>'
---

Well, class finally started up again.  Feels like its been a year since I've been to class, but its only been a month.  I'm looking forward to this software engineering class.

In the past week I ordered my books and went to class a couple times.  I'm attempting to complete the blog this semester.  Last semester I was in Downing's CS 371p Object-Oriented Programming, and started the blog, but didn't follow through and finish it.  The real bummer last semester is that had I completed the blog extra-credit, I would have gotten the highest score in the class.  I think that I'll have a little more time this semester, and last November was really busy.

I didn't have anything blocking my progress this week - I already had the educational accounts setup from last semester.  I hope it's not cheating, I went ahead and re-used my already setup blog from last semester.  I belive it meets all the requirements (jekyll-based blog, github pages, etc...), and of course I'll add new posts.  One new thing this semester is adding google analytics to the blog, which I already did.  I've used google analytics previously when I ran the website for an old employer.  I'm not completely sure of the benefit of setting it up on our blog, maybe its just to gain the experience, or maybe we'll have to set it up for the group project this semester.

Next week I'm going to start on the Collatz project (I might even start this afternoon).  I'm not worried about the project, its almost identical to the first project in CS 371p, except written in Python.  I'm actually very excited about having a class which uses Python.  When I took CS 371p, I already had experience using C++ and was at least familiar with most of the concepts presented in class.  I've used Python a few times at work, but I am not nearly as confident with Python as I am with C++.  Python is so different than C++ that I find it difficult to switch betwen them.

## Tip of the week ##

The blog requirements mention that the headshot must be rendered on the screen at least 1.75x2.00 inches.  Did you know that CSS accepts `in` as a unit, so you can actually make the size of the headshot 1.75 x 2 inches exactly.

On my blog I found the main.scss file and updated the `.header .header-logo img` entry like so:

      .header .header-logo img {
        text-align: center;
        margin: 0 auto; 
        width: 1.75in;
        height: 2in;
      }

I'm sure that the css to modify will vary based on the theme, you may have to search through the css or maybe even the html to find the style information for the headshot.
