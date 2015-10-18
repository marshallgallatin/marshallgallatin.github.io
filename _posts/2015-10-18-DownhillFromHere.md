---
layout: post
title: It's all downhill from here
banner_video: '<iframe width="854" height="480" src="https://www.youtube.com/embed/HgzGwKwLmgM" frameborder="0" allowfullscreen></iframe>'
---

Well, by my calculations we've reached and passed the midpoint of this class.  So far I've enjoyed the projects and the lectures.  Nothing in this class so far has been a surprise, expectations are clearly defined and grading seems to be perfectly reasonable.  We got our test grades back, and I'm happy with mine, but I'm still kicking myself over the dumb mistake I made on the last problem.

The allocator project was assigned last week, and I admit, it really doesn't look that hard.  Maybe I'm just crazy.  The most worrisome aspect of this project is the minimal details on the project page.  The requirements are pretty much "heres a start, build an allocator."  The unit tests really aren't super helpful either, the code we've been given passes the unit tests, so I guess its really up to us.  On the bright side, lecture on Monday was a good overview of what we are expected to do.  I dunno, I'm still not worried about this, maybe I should be.

It is a little difficult to get back into the swing of having a project.  We haven't had a project due for the past month, so at least the first part of this project is just dusting off the mothballs in my brain.  Personally, I wish the projects didn't let up for the test, then I wouldn't have the opportunity to even consider slacking off.  Not a big deal though.

## Tip of the week ##

We discussed new and delete this week (it was probably on Monday).  As I was leaving class I heard a student say something along the lines of "never use new and delete, just use the STL."  In my experience, this is far from true.  Yes, when possible use the stack and the standard library.  I'd never even consider writing my own vector implementation, but there are times where what you need isn't in the standard library.  There is a sorta "extended standard library" called [boost](http://www.boost.org/) which has even more containers and useful code (scoped_arrays are super helpful).  Even using boost, sometimes the use of new and delete is inevitable.  My tip of the week is to learn a programming technique called Resource Allocation Is Initialization, abbreviated RAII.  By using RAII with new and delete, one can prevent memory leaks even when exceptions are thrown.

For example, instead of writing code like this:

    void function (){
        try {
            T* p = new T();
            p->allocate(); } //something throws, this is just an example
        catch(...){ delete p; }
        ...}
        
Its much cleaner, and easier to do something like this:

    class t_wrapper
    {
        public:
            T* p;
            t_wrapper(): p(new T()) {}
            ~t_wrapper(){ delete p; }
    };
    
    void function (){
        t_wrapper tw;
        (tw.p)->allocate();
        ...}
        
This way, no matter what happens, the t_wrapper object will eventually leave scope and be destroyed, thus cleaning up the T object that was created on the heap.  This is really a super, super, super, simple example, which I took some shortcuts on just to illustrate the technique.  Honestly if you are doing anything this simple, something like a [std::unique_ptr](http://www.cplusplus.com/reference/memory/unique_ptr/) would be more appropriate.  RAII is also useful for much more than just new and delete - mutexes are one example.

RAII Links:

* [http://www.hackcraft.net/raii/](http://www.hackcraft.net/raii/)
* [http://en.cppreference.com/w/cpp/language/raii](http://en.cppreference.com/w/cpp/language/raii)
