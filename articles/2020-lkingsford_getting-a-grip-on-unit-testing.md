# Getting a basic grip on Unit Testing
**Lachlan Kingsford, 2020**

I've heard it said before that the only code that you can absolutely guarantee
works is code that you've deleted. As nice as it would be to make a career out
of deleting things, as software engineers, we do need to write new code. And, if
we want to be good at our jobs, that code should work and keep working.

This article is for new software engineers who haven't yet spent much time doing
testing, and for experienced engineers who would like to either brush up on the
basics or introduce testing to a new environment. It's going to be at a fairly
beginner level - but, it's my intention to go into a bit more specific technical
depth in future articles over the next few weeks.

I'm going to give a few small code examples in Python, but pretty much all of
the principles can be applied in whatever language you prefer.

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

## The 'whats' of Automated Testing

You know that thing we do as software engineers where we write code, so a
computer will do a tedious or complicated task so a person doesn't waste time
having to do it themselves, manually? It's that! But the person is you, and the
task is checking that your code does what it is supposed to.

There's lots of types of automated testing, and a few different ways we can use
it. I'm going to give a quick overview of them. Feel free to skip this part if
you've heard it before. We're also not going to cover all of these in detail -
but they're things you're going to see around, that it's helpful to understand.

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
integration test might include whether the wheel spins after turning the car
on, putting it in first, and pushing the accelerator.

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
treat TDD as a tool to use like a hammer, not as something that must be adhered
to religiously.

### Continuous Integration

Imagine you had automated tests. Now imagine that every time you push that code
to master, or create a pull request, it builds your code (if your language
needs it) and runs all your automated tests. Maybe it doesn't even let you
merge it into master unless it has automatically run the tests on your code.

That in essence in Continuous Integration. It's something useful that Automated
Tests enable, that I'll talk about in a lot more detail in a later article,
because it's a big subject by itself.

## Why automated testing?

Automated testing has a few obvious and less obvious benefits.

### It can help you make sure new code works

Programmers make mistakes. When we're lucky, we find those mistakes before
users do. You can manually test, but manually testing all the types of input
might be difficult or time consuming. Having automated tests can prove each
part of your code works, before a user proves that it doesn't.

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

### It can help you write more understandable code

I'll discuss this in this article, but writing your code in a way that is easy
to test encourages you to write small, well defined, understandable functions.

## Writing tests with Pytest

This is the section where things are going to start getting a bit more specific
to unit testing in Python.

### Installing Pytest

The first step of adding unit testing is adding your unit test framework. In
Python, I tend to use Pytest instead of the `unittest` module that's included
in Python's standard library - as it provides some useful out-of-the-box
features (like parametization), and I find the way that it structures tests
to be more intuitive.

I assume that you're using, and have activated a [virtual environment](https://docs.python.org/3/library/venv.html).
Using a virtual environment is an easy way to separate the projects that you
are working on, and the - potentially clashing - pip packages that are required
for them.

To install Pytest using pip, run the command `pip install pytest`.

Verify Pytest is installed by running the command `python -m pytest` in your
shell. You should see some output like the following:

```
(env) lachlan@Arnie:~/project_folder$ python -m pytest
================================================= test session starts ==================================================
platform linux -- Python 3.5.3, pytest-5.4.1, py-1.8.1, pluggy-0.13.1
rootdir: /home/lachlan/project_folder
collected 0 items

================================================ no tests ran in 0.03s =================================================
```

> We're running Pytest by `python -m pytest` intentionally here. Alternatively,
> you run `pytest` - but it that way requires some additional work. The basic
> reason is that just calling `pytest` changes the paths that Python searches
> for imports - see [here](https://stackoverflow.com/q/10253826/260095).
> Basically - I'm taking this route to make it as easy as possible to write
> some useful tests... and not stir up arguments by adding [an `__init__.py` file to the `tests` folder.](https://pytest.readthedocs.io/en/2.7.3/goodpractises.html#choosing-a-test-layout-import-rules)

### Writing a test

Pytest, by default, looks for tests recursively in the folder you start it in,
by looking for files called `test_*.py` or `*_test.py` ([see Pytest Test Discovery](https://docs.pytest.org/en/latest/goodpractices.html)]).

Beyond this, where you put your tests is up to you. Some engineers prefer to put
it in the same folders as their code. I prefer to have a `tests` folder which
mirrors the structure of folders for the rest of the source. Really - it's a
matter of what works for you and your team.

We're going to write a fairly common style test for our first test: we're going
to test a method in an (albeit simple) class.

Setup folders as follows:
```
    project_folder
        models
        tests
```

You need to have a virtual environment set up, with pip set up.

To try this, we need something to test. So, make the file `project_folder/models/user.py`
with the following:

```python
class User:
    """A user of the project"""
    def __init__(self,
                 username,
                 real_name = None,
                 preferred_name = None):
        self.username = username
        self.real_name = real_name
        self.preferred_name = preferred_name

    def get_display_name(self):
        """Return the name to display for this user.

        The users display name is their preferred_name if it is set,
        if not, their real_name if it is set, alternatively, if not,
        their username"""
        if self.preferred_name:
            return self.preferred_name
        if self.real_name:
            return self.real_name
        return self.username
```

As you can see, it's part of a class that might exist on a web application.
We're going to be testing the `get_display_name` method. I've defined the
requirements of the `get_display_name` function in the source there. It's
probably a little smaller than what you'll normally test, but it's probably
helpful as a learning exercise. Not to mention, we all make mistakes - even
with simple code - so it's probably still worthwhile checking that it works.

We're going to start by testing that when we have a user with a `username`,
`real_name` and `preferred_name`, that `get_display_name` returns the
`preferred_name`.

Put the following in `project_folder/tests/test_user.py`:

```python
import project_folder.models.user

def test_get_display_name(self):
    # GIVEN a User with a username, real name, and preferred name
    user = models.user.User("username", "real name", "preferred")
    # WHEN User.display_name is run
    display_name = user.get_displayed_name()
    # THEN it returns the preferred name
    assert display_name == "preferred", "Display name should have returned preferred name"
```

> You may not have seen the **GIVEN**, **WHEN**, **THEN** layout used in this
> unit test. It's borrowed from Dan North's [writings on Behavior-driven development](https://dannorth.net/introducing-bdd/).
> They can be a useful way of writing user stories/specifications for work, but
> they can also be helpful in explaining what piece of behavior a unit test is
> testing. After all - almost every test is going to consist of setting up the
> test (**GIVEN**), running the piece of code you're testing (**WHEN**) and 
> verifying that it did what you expected it to do (**THEN**).

You'll see this is essentially a very ordinary - and indeed short - function.
We create the object, we run the function, and then we do an `assert` to verify
that the function returned what we expect it to. The `assert` statement might
be new to you. It essentially means to do a logical test, and raise an
exception if it fails. You can include a more helpful error message after a
comma like I did there. I recommend you do so - when, in a years time you write
code that breaks this unit test, you know why. You will usually use at least
one `assert` in a test - but you don't need to.

Note that you didn't need to add any decorators. By having the test in a `.py`
file with a name starting with `test`, and a function with a name starting
with `test`, Pytest knew to run it.

Now, lets run the test. Call `python -m pytest` from your shell in
`project_folder`. You should see something like:

```
(env) lachlan@Arnie:~/project_folder$ python -m pytest
================================================= test session starts ==================================================
platform linux -- Python 3.5.3, pytest-5.4.1, py-1.8.1, pluggy-0.13.1
rootdir: /home/lachlan/project_folder
collected 1 item

tests/test_user.py .                                                                                             [100%]

================================================== 1 passed in 0.02s ===================================================
```

The test successfully ran! If you'd like, run `python -m pytest -v` and you'll
see a `PASSED` or `FAILED` result next to each individual test instead of a
summary.

### Making a test fail

Now - let's break it again, for learnings sake. Change `User.get_display_name`
in `models/user.py` to be the following:

```python
    def get_display_name(self):
        """Return the name to display for this user.

        The users display name is their preferred_name if it is set,
        if not, their real_name if it is set, alternatively, if not,
        their username"""
        return self.username
        if self.preferred_name:
            return self.preferred_name
        if self.real_name:
            return self.real_name
```

The enthusiastic coder may have already noted that this will always return the
username - which, we know will fail our test. So - let's run it again with
`python -m pytest`. You'll see something like:

```
(env) lachlan@Arnie:~/project_folder$ python -m pytest
================================================= test session starts ==================================================
platform linux -- Python 3.5.3, pytest-5.4.1, py-1.8.1, pluggy-0.13.1
rootdir: /home/lachlan/project_folder
collected 1 item

tests/test_user.py F                                                                                             [100%]

======================================================= FAILURES =======================================================
________________________________________________ test_get_display_name _________________________________________________

    def test_get_display_name():
        # GIVEN a User with a username, real name, and preferred name
        user = models.user.User("username", "real name", "preferred")
        # WHEN User.display_name is run
        display_name = user.get_display_name()
        # THEN it returns the preferred name
>       assert display_name == "preferred", "Display name should have returned preferred name"
E       AssertionError: Display name should have returned preferred name
E       assert 'username' == 'preferred'
E         - preferred
E         + username

tests/test_user.py:9: AssertionError
=============================================== short test summary info ================================================
FAILED tests/test_user.py::test_get_display_name - AssertionError: Display name should have returned preferred name
================================================== 1 failed in 0.08s ===================================================
```

Unit tests will usually fail because of an `assert` that failed, or an exception
that was thrown during the test. As you can see here, you'll see that a test
failed, and a stack trace as to how.

If your project uses Python's `logging` library, any log messages that are a
higher level than `WARNING` will be shown with any failing tests.

### Parametrizing Tests

Before we start, we're going to need to fix `User.get_display_name` (since we
intentionally broke the model in the last test). Change `User.get_display_name`
in `models/user.py` back to the following:

```python
    def get_display_name(self):
        """Return the name to display for this user.

        The users display name is their preferred_name if it is set,
        if not, their real_name if it is set, alternatively, if not,
        their username"""
        if self.preferred_name:
            return self.preferred_name
        if self.real_name:
            return self.real_name
        return self.username
```

Our first test only tested one specific part of the specification - namely that
`preferred_name` would be returned if `User` has a `real_name`, `preferred_name`
and `username`. This obviously would not pick up some bugs. For instance, if
the function wrongly *always* returned `preferred_name` (like we forced it to
always return `username` when we intentionally broke it) then that unit test
would pass.

The obvious solution is that we need to duplicate the test a bunch of times
with all of the potential combinations. So - with over-application of our
'Copy' and 'Paste' functions, we'll end up with four different copies of our
test function, that could test the entire spec. That's not too ludicrous,
but there is a better way.

After all - if you want to test your string manipulation function with the
entire [Big List of Naughty Strings](https://github.com/minimaxir/big-list-of-naughty-strings)
you probably don't want to make a few hundred copies of the same function.

Lots of testing frameworks - including Pytest - support a feature called
'parametrization'. This means that can get data from an iterator and run a
test with that data

> Yes, you can use generators - as your can use any iterators. It's outside the
> scope of this article to discuss exactly how. The official documentation for
> Pytest has a [pretty in-depth look at how you can use parametrization](http://doc.pytest.org/en/latest/example/parametrize.html)
> that is worth a read.

So - here is our new version of `tests/test_user.py` that uses parametrization
to test all combinations of input.

```python
import pytest
import models.user


TEST_DISPLAY_NAME_DATA = [
    ['username', None,None, 'username'],
    ['username', 'real_name', None, 'real_name'],
    ['username', 'real_name', 'preferred_name', 'preferred_name'],
    ['username', None, 'preferred_name', 'preferred_name'],
]

@pytest.mark.parametrize("username,real_name,preferred_name,expected", TEST_DISPLAY_NAME_DATA)
def test_get_display_name(username, real_name, preferred_name, expected):
    # GIVEN a User with a username, real name, and preferred name
    user = models.user.User(username, real_name, preferred_name)
    # WHEN User.display_name is run
    display_name = user.get_display_name()
    # THEN it returns the preferred name
    assert display_name == expected, "Display name returned incorrect name"
```

The magic part is the `@pytest.mark.parametrize`. It marks the test for Pytest
as one that uses parametrization. The first parameter (`"username,real_name,preferred_name,expected"`)
is a list of the parameters on the test function to fill in. The second
parameter is the actual data source.

The data source in this case is `TEST_DISPLAY_NAME_DATA`. It's a list of lists.
Each item in the list is one of the test cases. Each item in those lists is one
of the parameters to fill in.

It's quite common to have a field like `expected`, because often you're testing
for specific output for each input value. Sometimes you may not need it - for
instance, if you're testing that none of your test cases causes the code you're
testing to raise an exception.

We now run these tests the same way:

```
(env) lachlan@Arnie:~/project_folder$ python -m pytest
================================================= test session starts ==================================================
platform linux -- Python 3.5.3, pytest-5.4.1, py-1.8.1, pluggy-0.13.1
rootdir: /home/lachlan/project_folder
collected 4 items

tests/test_user.py ....                                                                                          [100%]

================================================== 4 passed in 0.04s ===================================================
```

As you can see, it now ran the four different tests. If one of those failed, you
would see which test case failed so you can work to making it pass.

## In conclusion

1. Unit testing is useful.
2. It's not that hard to do with Pytest.
3. When you've got to test a bunch of cases, `parametrize` is useful.