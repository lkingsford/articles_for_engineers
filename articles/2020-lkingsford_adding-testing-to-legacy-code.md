# Adding testing to legacy code
**Lachlan Kingsford, 2020**

This is my third article on on Automated Testing. If you haven't used Pytest
before, I highly recommend looking at [my previous article](2020-lkingsford_writing-your-first-unit-test-with-pytest.md)
before working through this article.

Now that I've made you all excited about automated testing, you may have taken
the next step and started adding some unit tests to your current projects. If
so - well done! If you succeeded in part or whole, great! But, you also may
have faced some issues with testing classes and methods that rely on other
parts of your code, or do lots of things.

In this article, I'm going to show you some of the techniques that I use to
test parts of code that weren't designed for testing. I'm also going to point
out some design decisions that could make unit testing easier. I'm also going
to show you one specific tool that Pytest offers - mocking - that may help
you test some of these convoluted or complex test.

## What's next?

Like in the last article, the best way you can make use of this knowledge is by
attempting to write tests for one of the more complicated parts of your current
project.

The next article is going to start addressing some integration tests. Because
they're helpful, particularly with integration tests, I'm also going to show
you how to work with fixtures.


> This article is published under the [Creative Commons Attribution Non-Commercial ShareAlike 4.0 International license](https://creativecommons.org/licenses/by-nc-sa/4.0/).
>
> Commercial licensing is available for this article. Please contact Lachlan
> Kingsford at licensing@nerdygentleman.com for more information.