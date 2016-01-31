---
layout: post
title: Calm before the storm
---

First the required questions, then the commentary.

### What did you do this past week? ###

This week I read most of the required reading, and started on the collatz project.  I've gotten the repository setup, setup the issues, and implemented the simplest version of the project.  I have not tried submitting to Sphere yet, but I might try before the end of the day.

### What's in your way? ###

Right now there is nothing in CS 373 thats in my way.  All my roadblocks have to do with work.  We had a big push to get a project done last week, which meant some late days and not much freetime for homework.  I'm thinking things will free up some next week (if time doesn't free up, I'll have to make some time - I'll just sleep less.  Who needs sleep anyway?)

### What will you do next week? ###

Next week I had better finish the collatz project.  I was in Object Oriented Prgramming (OOP) last semester, and after my experience there, I'm not really worried about completing the project.  I'm going to try to catch up on the reading some too.  The non-textbook readings are the same from OOP so on the bright side I can generally skim over those and re-remember.

So far this class has taken a very similar path that OOP clas did: assertions, unit tests, code coverage, and how to use those appropriately.  I'm really hoping that the class diverges soon, otherwise I'm worried that the class is going to get boring.  

When it comes to the projects, I still don't understand the "3 unit tests per method/function" requirement.  I feel like this discourages the good programing practice of breaking up large functions into smaller more managable ones.  I don't know, maybe the graders see a single monolithic function and deduct design points, but it seems that writing a single monolithic function would be a way to game the system and write fewer unit tests.

## Tip of the week ##

You should learn [make](https://www.gnu.org/software/make/manual/) if you haven't already.  It is very quirky and kinda weird (spaces and tabs are treated differently), but a good makefile is impossible to beat.  One of the things I noticed, is that the given makefile for the collatz project doesn't put a dependency on the Collatz.py file for the "*.tmp" recipes.  This means if you've already run `make test`, if you change just the Collatz.py file, and then run `make test` again - the tests won't run again without running `make clean` before `make test`.

My tip of the week is add Collatz.py to the .tmp recipes, this way, if you change just the Collatz.py file, the tests are guaranteed to rebuild every time when you run `make test`.
