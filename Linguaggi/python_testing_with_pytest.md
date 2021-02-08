# Python Testing with pytest

Brian Okken

- [Book reviews on GoodReads.com](https://www.goodreads.com/book/show/34695799-python-testing-with-pytest)

## Writing Test Functions

### Project task_proj

Before to run pytest you should install locally tasks using pip.

If you want to be able to modify the source code while tasks is installed, you need to install it with the -e option:

>pip install -e ./task_proj/

### assert statements

With pytest, you can use **assert "expression"** with any expression. If the expression would evaluate to False if converted to a bool, the test would fail.

pytest includes a feature called assert rewriting that intercepts assert calls and replaces them with something that can tell you more about why your assertions failed.

### Marking Test Functions

A test can have more than one marker, and a marker can be on multiple tests.

>@mark.pytest.smoke
mark a test as "smoke".

For example:

>pytest -v -m 'smoke' ./task_proj/tests/func/test_api_exceptions.py

or:

>pytest -v -m 'smoke'

#### Built-in Fixture

>@pytest.fixture(autouse=True) use a builtin fixture

### Skipping Tests

pytest includes a few helpful builtin markers.

The skip and skipif markers enable you to skip tests you don't want to run.

>@pytest.mark.skip(reason='description of the reason')

or

>@pytest.mark.skipif(tasks.__version__<'0.2.0',
reason='not supported until version 0.2.0')

pytest -rs visualize the reason of skip tests

### Marking Tests as Expecting to Fail

>@pytest.mark.xfail(tasks.__version__<'0.2.0',
reason='not supported until version 0.2.0')

### Running a Subset of Tests

#### A Single Directory

>pytest directory_path --tb=no

#### A Single Test File/Module

>pytest file_path

#### A Single Test Function

>pytest -v file_path::function_name

#### A Single Test Class

>pytest -v file_path::ClassName

#### A Single Test Method of a Test Class

>pytest -v file_path::ClassName::method_name

#### A Set of Tests Based on Test Name

>pytest -v -k key_in_their_name

### Parametrized Testing

Is a way to send multiple sets of data through the same test and have pytest report if any of the sets failed.

#### Single parameter

>@pytest.mark.parametrize('task',
[Task('sleep', done=True),
...
])
>
>def test_add_2(task):

or

>tasks_to_try = [Task('sleep', done=True),
Task('wake', 'brian'),
Task('breathe', 'BRIAN', True),
Task('exercise', 'BrIaN', False)]
>
>@pytest.mark.parametrize('task', tasks_to_try)
>
>def test_add_4(task):

#### Multi-parameter

>@pytest.mark.parametrize('summary,owner,done',
[('sleep', None, False),
('wake', 'brian', False),
...
])
>
>def test_add_3(summary, owner, done):

or

>task_ids = ['Task({},{},{})'.format(t.summary, t.owner, t.done)
for t in tasks_to_try]
>
>@pytest.mark.parametrize('task', tasks_to_try, ids=task_ids)
>
>def test_add_5(task):

#### To class parameter

>tasks_to_try = [Task('sleep', done=True),
Task('wake', 'brian'),
Task('breathe', 'BRIAN', True),
Task('exercise', 'BrIaN', False)]
>
>task_ids = ['Task({},{},{})'.format(t.summary, t.owner, t.done)
for t in tasks_to_try]
>
>@pytest.mark.parametrize('task', tasks_to_try, ids=task_ids)
>
>class TestAdd():

#### Identify parameters by including an id right alongside the parameter value

>@pytest.mark.parametrize('task',
>
>[
>
> pytest.param(Task('create'), id='just summary'),
>
> pytest.param(Task('inspire', 'Michelle'), id='summary/owner'),
>
> pytest.param(Task('encourage', 'Michelle', True), id='summary/owner/done')
>
>])
>
>def test_add_6(task):

## pytest Fixtures

Fixtures are functions that are run by pytest before, or after, the actual test functions.

The code in the fixture can do whatever you want it to.

When you include the fixture name in the parameter list of a test function, pytest knows to run it before running the test.

### Sharing Fixtures Through conftest.py

To share fixtures among multiple test files, you need to use a **conftest.py** file somewhere centrally located for all of the tests.

Although conftest.py is a Python module, it should not be imported by test files.
This file gets read by pytest, and is considered a local *plugin*.

### Using Fixtures for Setup and Teardown

### Tracing Fixture Execution with -setup-show

>pytest --setup-show test_add.py -k valid_id

### Using Fixtures for Test Data

If the assert (or any exception) happens in the fixture, a couple of things happen:

- the stack trace shows correctly that the assert happened in the fixture function.
- the test is reported not as Fail, but as *Error*

### Using Multiple Fixtures

One of the great reasons to use fixture:

- to focus the test on what you're actually testing, not on what you had to do get ready for the test.

You can using comments for:

- GIVEN
- WHEN
- THEN

and trying to push as much GIVEN into fixtures for two reasons:

1. it makes the test more readable and maintainable
2. an assert or exception in the fixture results in an ERROR, while an assert or exception in a test function results in a FAIL.

>pytest --setup-show test_module_name.py::test_function_name

### Specifying Fixture Scope

The optional **scope** parameter to @pytest.fixture() can have the values of:

- function
- class
- module
- session

The default scope is function.

#### Function scope

Run once per test function.
The setup portion is run before each test using the fixture.
The teardown portion is run after each test using the fixture.

#### Class scope

Run once per test class.

#### Module scope

Run once per module.

#### Session scope

Run once per session.
All test method and functions using a fixture of session scope share one setup and teardown call.

The scope is defined with the fixture.

The scope is set at the definition of a fixture, and not at the place where it's called.

Fixtures can only depend on other fixtures of their same scope or wider.

#### Changing Scope for Tasks Project Fixtures

To have something like tasks_db be session scope, you need to use tmpdir_factory, since tmpdir is function scope and tmpdir_factory is session scope.
Luckily, this is just a one-line code change.

On file conftest.py we changed task_db to depend on tasks_db_session, and we deleted all the entries to make sure it's empty. Because we didn't change its name, none of the fixtures or tests that already include it have to change.

The data fixtures just return a value, so there really is no reason to have them run all the time.

Once per session is sufficient.

### Specifying Fixtures with usefixtures

You can mark a test or a class with

>@pytest.mark.userfixtures('fixture1', 'fixture2')

userfixtures takes a string that is composed of a comma-separated list of fixtures to use.
It doesn't make sense to do this with test functions.
But it does work well for test classes.

```python
@pytest.mark.userfixtures('fixture1', 'fixture2')
class TestSomething():
    """ Demo class scope fixtures """
    pass

    def test_3(self):
        """ Test using a class scope fixture """
```

N.B. The test can use the return value of a fixture only if it's specified in the parameter list.

### Using autouse for Fixtures That Always Get Used

You can use autouse=True to get a fixture to run all of the time.

The autouse feature is good to have around. But it's more of an exception than a rule. Opt for named fixtures unless you have a really great reason not to.

### Renaming Fixtures

pytest allows you to rename fixtures with a name parameter to @pytest.fixture():

```python
@pytest.fixture(name='new_name')
```

### Parametrizing Fixtures

We can parametrize fixtures like as we parametrize the tests.

#### Parametrizing Fixtures in the Tasks Project

Example

## Builtin Fixtures

The builtin fixtures that come prepackaged with pytest can help you do some pretty useful things in your tests easily and consistently.

For example, pytest includes builtin fixtures to:

- handling temporary files
- access command-line options
- communicate between tests sessions
- validate output streams
- modify environmental variables
- interrogate warnings

### Using tmpdir and tmpdir_factory

The tmpdir and tmpdir_factory builtin fixtures are used to create a temporary file system directory before your tests run, and remove the directory when your tests is finished.

The tmpdir fixture has function scope, and tmpdir_factory fixture has session scope.

The value returned from tmpdir is an object of type py.path.local

#### Using Temporary Directories for Other Scopes

If we need a module or a class scope temporary directory, we create another fixture of the scope we want and have it use tmpdir_factory.

### Using pytestconfig

You can control how pytest runs through:

- command-line arguments and options
- configuration files
- plugins
- directory from which you launched pytest

The pytestconfig fixture is a shortcut to request.config.

### Using cache

When we do want to pass information to future test sessions, we can do it with the cache builtin fixture.

The cache fixture is all about storing information about one test session and retrieving in the next.

A great example is the builtin functionality of:

- --last-failed
- --failed-first

​$ ​​pytest​​ ​​--help​
​ ​...​
​ --lf, --last-failed rerun only the tests that failed at the last run (or
​ all if none failed)
​ --ff, --failed-first run all tests but run the last failures first. This
​ may re-order tests and thus lead to repeated fixture
​ setup/teardown
​ --cache-show show cache contents, don't perform collection or tests
​ --cache-clear remove all cache contents at start of test run.
​ ​...

You can see the stored information with:

>pytest --cache-show

or you can look in the cache dir:

>$ cat .cache/v/cache/lastfailed

You can pass in --clear-cache to clear the cache before the session.

The cache can be used to make a fixture that records how long tests take, saves the times, and on the next run, reports an error on tests that take longer than, say, twice as long as last time.

The interface for the cache fixture is simply:

```python
cache.get(key, default)
cache.set(key, value)
```

By convention, key names start with the name of your application or plugin, followed by a /, and continuing to separate sections of the key name with /'s.

### Using capsys

capsys provides two bits of functionality:

- it allows you to retrieve stdout and stderr from some code (**capsys.readouterr()**)
- it disables output capture temporarily

pytest usually captures the output from your tests and the code under test.
This includes print statements.

The -s option turns off this feature, and output is sent to stdout while the tests are running.

You can use **capsys.disabled()** to temporarily let output get past the capture mechanism.

### Using monkeypatch

A "monkey patch" is a dynamic modification of a class or module during runtime.
During testing, it is a convenient way to take over part of the runtime environment of the code under test and replace either input or output dependencies with objects or functions that are more convenient for testing.

The **monkeypatch** fixture provides the following functions:

- setattr(target, name, value=<noset>, raising=True): Set an attribute
- delattr(target, name=<noset>, raising=True): Delete an attribute
- setitem(dic, name, value): Set a dictionary item
- delitem(dic, name, raising=True): Delete a dictionary entry
- setenv(name, value, prepend=None): Set an environmental variable
- delenv(name, raising=True): Delete an environmental variable
- syspath_prepend(path): Prepend path to sys.path, which is Python's list of import locations
- chdir(path): Change the current working directory

You can also use the monkeypatch fixture functions in conjunction with unittest.mock to temporarily replace attributes with mock objects.

### Using doctest_namespace

You can have pytest look for and run doctest tests within your Python code by using the --doctest-modules flag.

With the doctest_namespace builtin fixture, you can build autouse fixtures to add symbols to the namespace pytest uses while running doctest tests. This allows docstring to be much more readable.

doctest_namespace is commonly used to add module imports into the namespace, especially when Python convention is to shorten the module or package name.

### Using recwarn

The recwarn builtin fixture is used to examine warnings generated by code under test. In Python you can add warnings that work a lot like assertions, but are used for things that don't need to stop execution.

The recwarn value acts like a list of warnigs, and each warning in the list has a category, message, filename, and lineno defined.

The warnings are collected at the beginning of the test. You can use recwarn.clear() to clear out the list before the chunck of the test where you do care about collecting warnings.

In addition to recwarn, pytest can check for warnings with pytest.warns().
The pytest.warns() context manager provides an elegant way to demark what portion of the code you're checking warnings.
