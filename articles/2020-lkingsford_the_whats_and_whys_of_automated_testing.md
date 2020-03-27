# The 'Whats' and 'Whys' of Automated Testing
**Lachlan Kingsford, 2020**

I've heard it said before that the only code that you can absolutely guarantee
works is code that you've deleted. As nice as it would be to make a career out
of deleting things, as software engineers, we do need to write new code. And, if
we want to be good at our jobs, that code should work and keep working.

This is the first article in a series I'm going to be writing on automated
testing. Later articles are going to have an emphasis on testing with Pytest in
Python, but will hopefully still be valuable for engineers who work in other
languages. Lots of the principles are fairly universal.

I'm starting from absolute scratch. This article introduces some of the
terminology around automated testing, and suggests some reasons why you might
get benefit from automated testing. Later articles will go into specific details
on how to add automated testing to your projects.

Before we start, a quick introduction: Hi. I'm Lachlan Kingsford. I'm a
software engineer with 5 years of experience. I've worked on the front and back
end of web applications, in systems programming on IoT devices, and desktop
applications. I've done most of my professional work in C# and Python, with a
bit of Javascript, C and C++ to boot. I'm also a Scrum.org PSM 1 certified
scrum master. I've made the journey from being a programmer who was confident
that my ways were good enough, to one who embraced software methodology and
tooling once I saw how it was helping my team to regularly deliver good code.
I actually care about this stuff, and I believe in it, and I genuinely want to
help teams productively make cool things.

Let's get this show on the road!

## The 'Whats' of Automated Testing

You know that thing we do as software engineers where we write code, so a
computer will do a tedious or complicated task so a person doesn't waste time
having to do it themselves, manually? It's that! But the person is you, and the
task is checking that your code does what it is supposed to.

There's lots of types of automated testing, and a few different ways we can use
it. I'm going to give a quick overview of them. We're also not going to cover
all of these in detail - but they're things you're going to see around, that
it's helpful to understand.

### Automated testing

Getting a computer to run through a bunch of code for you so that you can check
that it works, without having to manually test each bit.

### Unit tests

Tests that are focused on testing one specific piece of the codebase. Usually,
a unit test will only test one specific function or group of functions. Ideally
unit tests should rely on no third party resources (like the database), but
pragmatically, that may not always be possible.

Unit tests specifically are the main focus of this article.

### Integration tests

Tests that test the codebase in a more complete way. Integration tests will
often rely on a development database\*, or third party APIs - preferably in a
development mode. They test how the codebase fits together. To use an analogy -
if your unit test for building a car tests the wheel can spin, then an
integration test might include whether the wheel spins when the engine is
running and the car is in gear.

> \* Yes, your integration tests should run on a development database, not your
> production database. In fact - why are you connected to prod at all with your
> untested code, huh? If you want to see a really good example of why you want
> your integration tests to run separately to prod, checkout [this article by David Cassel](https://thenewstack.io/junior-dev-deleted-production-database/)
> about one time it went very wrong.

### End-to-end tests

These are the big ones. These test that the application as a whole works - from
the user interface downwards. An example of one of these is a test that runs
your API, preloads a database, serves your front-end files, starts a scriptable
browser (like [Selenium](https://www.selenium.dev/)), and uses the browser to
log in to your web-app.

These can be worthwhile, but they can also be more costly in a few respects:
they often require more resources to run than unit or integration tests, are
usually slower to run than unit or integration tests, and can be more fragile
due to being sensitive to changes in the user experience. They have their
place, but my opinion is that developers writing unit or integration tests will
*usually* provide more value to the product.

### Test driven development

Test driven development (TDD) is a methodology where *all* code is written by
writing the tests first. You write tests to test all the functionality that
needs to exist, and then write the code specifically to make those tests pass.

I'm a bit more pragmatic. I think writing tests first is *sometimes* helpful.
For instance, often if I'm implementing an API - I'll write a basic test of
an ordinary use case before implementing it, or if I'm trying to fix a bug,
I'll write a test of what the correct behavior is so I can watch it break. I
treat TDD as a tool to use like a hammer - not as something that must be adhered
to religiously.

### Continuous integration

Imagine you had automated tests. Now imagine that every time you push that code
to master, or create a pull request, it builds your code (if your language
needs it) and runs all your automated tests. Maybe it doesn't even let you
merge it into master unless it has automatically run the tests on your code.

That in essence in Continuous Integration. It's something useful that Automated
Tests enable, that I'll talk about in a lot more detail in a later article,
because it's a big subject by itself.

If you're in a particularly excitable mood, you can extend this further into
Continuous Deployment where you have a system that automatically deploys any
code merged into master that has passed its automated tests. I'm not going to
go into detail about how to implement Continuous Deployment, or whether it's a
good idea for your project or team.

### Test coverage

Test coverage (or 'code coverage') is the amount of your code base that has
automated testing applied to it against the total size of your code base. A
code base that has 80% test coverage would have 80% of its lines of code run
during at least one test. Most unit testing tools and frameworks (like Pytest)
offer a way to calculate your test coverage.

Some teams aim for 100% test coverage. This is admirable, but again - I'm a
little more pragmatic. I would suggest that automated testing can have
diminishing returns. Personally, my preference remains to calculate test
coverage as a useful metric, but focus more heavily on whether specifications
are being tested than trying to achieve a specific number.

## Why automated testing?

Automated testing has a few obvious and less obvious benefits.

### It can help you make sure new code works

Programmers make mistakes. When we're lucky, we find those mistakes before
users do. You can manually test, but manually testing all the types of input
might be difficult or time consuming. Having automated tests can prove each
part of your code works, before a user proves that it doesn't.

When you're working in a team environment, it can also assist your team in
verifying that your new code works before it is merged into your Master branch -
particularly if you're using Continuous Integration.

### It can help you make sure that new code doesn't break old code

Often, the process of adding a feature, or refactoring some code will have
wider effects than what the programmer realizes at the time. For instance, I've
been working on legacy code when I've changed a function that I thought was
internal to a module to discover that I break an unrelated piece of code that
was relying on that function. Automated tests are not usually deleted until
their functionality is no longer required. So, if that code was tested, the test
would have failed alerting me that I broke a feature before the change was
accepted.

Basically, having automated tests means that code can keep working the way it
was intended to work during that long tail of maintenance.

### It can help you understand a bug

I hinted at this earlier, but a great way to figure out a bug is to write a
test for the correct behavior - which will naturally fail, but gives you a way
to either step through the code, or see exactly *how* the code is failing.

Having thorough unit tests will also verify which parts of the code still pass
unit tests, and therefore are not effected by the bug. Knowing what still works
can sometimes be just as helpful as knowing what is broken.

### It can help you write more understandable code

I'll discuss this in a later article, but writing your code in a way that is
easy to test encourages you to write small, well defined, understandable
functions. It also discourages you from relying on:

  - Side effects
  - Implicit behaviour in the functions you call
  - Over-reliance on global or object state
  - Tight coupling between objects

as these all make writing unit tests (and other automated tests) more difficult.

## What's next?

So, in my next article, I'm going to help you get started with Pytest. We're
going to set it up, write a unit test together, and then explore one of the
tools that Pytest offers: Parametrization.

> This article is published under the [Creative Commons Attribution Non-Commercial ShareAlike 4.0 International license](https://creativecommons.org/licenses/by-nc-sa/4.0/).
>
> Commercial licensing is available for this article. Please contact Lachlan
> Kingsford at licensing@nerdygentleman.com for more information.