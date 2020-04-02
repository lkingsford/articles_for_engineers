# Writing your first unit test with Pytest
**Lachlan Kingsford 2020**

Welcome to the second article in my series on Automated Testing.

In my [last article](2020-lkingsford_the_whats_and_whys_of_automated_testing.md),
I spoke at some length about the terminology used around automated testing, and
made some arguments about why you should be using automated testing.

In this article, I'm going to be getting on the tools to show you how to
actually get started with unit testing. I'm going to cover enough for you to
write your first unit test with Pytest, and then I'm going to show you how to
use parametization - which is one of my favorite tools to use to write useful
unit tests.

This is a very practical tutorial. If you haven't worked with Pytest before,
you're going to get the most out of it by actually working through the tutorial
by writing out the code and running it on your computer.

## Installing Pytest

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

## Writing a test

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

## Making a test fail

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

## Parametrizing Tests

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

## What's next?

At the point, you should have enough tools to start adding some unit tests to
your own projects, and start making them more reliable. Pick one of your python
projects, and add a unit test or two - you'll gain far more out of using this
and thinking through the process than by just reading articles.

But, I recognize that it's not always easy to add unit testing to a legacy
project. In my next article, I'm going to work through some strategies for
adding unit tests to legacy projects generally, and some tools that Pytest
provides that can help you do so.

> This article is published under the [Creative Commons Attribution Non-Commercial ShareAlike 4.0 International license](https://creativecommons.org/licenses/by-nc-sa/4.0/).
>
> Commercial licensing is available for this article. Please contact Lachlan
> Kingsford at licensing@nerdygentleman.com for more information.