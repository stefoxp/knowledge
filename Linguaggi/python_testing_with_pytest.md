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

- setattr(target, name, value=noset, raising=True): Set an attribute
- delattr(target, name=noset, raising=True): Delete an attribute
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

## Plugins

### Finding Plugins

- [https://docs.pytest.org/en/latest/plugins.html](https://docs.pytest.org/en/latest/plugins.html)
- [https://pypi.python.org](https://pypi.python.org)
- [https://github.com/pytest-dev](https://github.com/pytest-dev)

### Installing Plugins

#### Install from PyPI

>pip install pytest-cov

#### Install a Particular Version from  PyPI

>pip install pytest-cov==2.4.0

#### Install from a .tar.gz or .whl File

>pip install pytest-cov-2.4.0.tar.gz

#### Install from a Local Directory

You can keep a local stash of plugins in a local or shared directory in .tar.gz or .whl format.

--no-index option tells pip to not connect to PyPI.

#### Install from a Git Repository

### Writing Your Own Plugins

A handful of fixtures that you want to share between a couple of projects can be shared easily by creating a plugin.

Plugin can include hook functions that alter pytest's behavior.

### Creating an Installable Plugin

We need to create a new directory to put our plugin code.

```bash
# for example

pytest-nice
​ ├── LICENCE
​ ├── README.rst
​ ├── pytest_nice.py
​ ├── setup.py
​ └── tests
​ ├── conftest.py
​ └── test_nice.py
```

In **pytest_nice.py**, we'll put the exact contents of our conftest.py that were related to this feature.

In **setup.py**, we need a very minimal call to setup().
All the parameters to setup() are standard and used for all Python installers.
The piece that is different for pytest plugin is the entry_points parameter:

entry_points={'pytest11': ['nice = pytest_nice', ], },

pytest11 is a special identifier that pytest looks for.
With this line, we are telling pytest that nice is the name of our plugin, and pytest_nice is the name of the module where our plugin lives.

Some form of **README** is a requirement by setuptools.

### Testing Plugins

Plugins are code that needs to be tested just like any other code.

We can test in an automated way using a plugin called **pytester** that ships with pytest but is disabled by default.

Our test directory for pytest-nice has two files: conftest.py and test_nice.py.

To use pytester, we need to add to conftest.py:

```python
pytest_plugins = 'pytester'
```

This turns on the pytester plugin.

The process of testing is:

- make an example test file
- run pytest with or without some options in the directory that contains our example file
- examine the output
- possibly check the result code - 0 for all passing, 1 for some failing

The **testdir** fixture automatically creates a temporary directory for us to put test files.
It has a method called **makepyfile()** that allows us to put in the contents of a test file.

We run pytest against the new test file with **testdir.runpytest()**.
The return value can then be examined further, and is of type *RunResult*.

Usually, I look at stdout and ret.
For checking the output like we did manually, use **fnmatch_lines()**, passing in a list of strings that we want to see in the output, and then making sure that ret is 0 for passing sessions and 1 for failing sessions.

To run the tests, let's start in our pytest-nice directory and make sure our plugin is installed:

> $ ..../pytest-nice/ pip install .

### Creating a Distribution

From the command line, we can use this setup.py file to create a distribution:

> $ python setup.py sdist

#### Distributing Plugins Through s Shared Directory

#### Distributing Plugins Through PyPI

## Configuration

### Understanding pytest Configuration Files

- **pytest.ini**: is the primary pytest configuration file that allows you to change default behavior.
- **conftest.py**: is a local plugin to allow hook functions and fixtures for the directory where the conftest.py file exists and all subdirectories.
- **__init__.py**: into every test subdirectory, this file allows you to have identical test filenames in multiple test directories.

If you use **tox**: tox.ini

If you want to distribute a Python package:

- **setup.cfg**: is a file that's also in ini file format and affects the behavior of setup.py

Regardless of which file you put your pytest configuration in, the format will mostly be the same.

```ini
pytest.ini

[pytest]
addopts = -rsxX -l --tb=short --strict
xfail_strict = true
... more options ...
```

```cfg
... packaging specific stuff ...
setup.cfg

[tool:pytest]
addopts = -rsxX -l --tb=short --strict
xfail_strict = true
... more options ...
```

#### List the Valid ini-file Options with pytest --help

#### Plugins Can Add ini-file Options

### Changing the Default Command-Line Options

If you set addopts in pytest.ini to the options you want, you don't have to type them in anymore.

> addopts = -rsxX -l --tb=short --strict

- -rsxX tells pytest to report the reasons for all tests that skipped, xfailed, or xpassed.
- -l tells pytest to report the local variables for every failure with the stacktrace
- --tb=short removes a lot of the stack trace
- --strict disallows markers to be used if they aren't registered in a config file.

### Registering Markers to Avoid Marker Typos

It is easy to misspell a marker and end up having some tests marked with @pytest.mark.smoke and some marked with @pytest.mark.somke. This isn't an error. pytest just thinks you created two markers.

This can be fixed by registering markers in pytest.ini:

```ini
pytest.ini

[pytest]
markers =
smoke: Run the smoke test functions for task project
get: Run the test functions that test tasks.get()
```

With these markers registered, you can now also see them with pytest --markers with their descriptions:

> $ pytest --markers

With them registered, and if you use --strict, any misspell or unregistered markers show up as an error.

### Requiring a Minimum pytest Version (only >= 3.0)

```ini
pytest.ini

[pytest]
minversion = 3.0
```

### Stopping pytest from Looking in the Wrong Places

All directories *starting with a dot* will not be traversed.

```ini
pytest.ini

[pytest]
norecursedirs = .* venv src *.egg dist build
```

When overriding a setting that already has a useful value, like this setting, it's a good idea to know what the defaults are and put the ones back you care about (es. *.egg dist build).

### Specifying Test Directory Locations

testpaths tells pytest where to look. It is a list of directories relative to the root directory to look in for tests. It's only used if a directory, file, or nodeid is not given as an argument.

This setting doesn't help much with interactive testing but it's great for tests launched from a continuous integration server.

### Changing Test Discovery Rules

The standard test discovery rules are:

- start at one or more directory
- look in the directory and all subdirectories recursively for test modules
- a test module is a file with a name that looks like test_*.py or *_test.py
- look in test modules for functions that start with test_
- look for classes that start with Test. Look for methods in those classes that start with test_ but don't have an __init__ method

You can change this rules.

#### python_classes

```ini
pytest.ini

[pytest]
python_classes = *Test Test* *Suite
```

#### python_files

```ini
pytest.ini

[pytest]
python_files = test_* *_test check_*
```

#### python_functions

```ini
pytest.ini

[pytest]
python_functions = test_* check_*
```

### Disallowing XPASS

Setting xfail_strict = true causes tests marked with @pytest.mark.xfail that don't fail to be reported as an error.

### Avoiding Filename Collisions

If you have __init__.py file in all of your test subdirectories, you can have the same test filename show up in multiple directories.

## Using pytest with Other Tools

### pdb: Debugging Test Failures

You use --pdb to have pytest start a debugging session at the point of failure.

pytest options available to help speed up debugging test failures:

- --tb=[auto/long/short/line/native/no]: Controls the traceback style.
- -v / --verbose: Displays all the test names, passing or failing.
- -l / --showlocals: Displays local variables alongside the stacktrace.
- -lf / --last-failed: Runs just the tests that failed last.
- -x / --exitfirst: Stops the tests session with the first failure.
- --pdb: Starts an interactive debugging session at the point of failure.

### Coverage.py: Determining How Much Code Is Tested

Code coverage tools are great for telling you which parts of the system are being completely missed by tests.

**Coverage.py** is the preferred Python coverage tool that measures code coverage.

You need install it and install a plugin called **pytest-cov** that will allow you to call coverage.py from pytest with some extra pytest options.

run our baseline coverage report:

> $ pytest --cov=src

generate an HTML report:

> $ pytest --cov=src --cov-report=html

While code coverage tools are extremely useful, striving for 100% coverage can be dangerous.

### mock: Swapping Out Part of the System

Mock objects are sometimes called test doubles, spies, fakes, or stubs.

For use with pytest, a plugin called **pytest-mock** has some conveniences that make it my preferred interface to the mock system.

### tox: Testing Multiple Configurations

tox is a command-line tool that allows you to run your complete suite of tests in multiple environments.

tox uses the setup.py file for the package under test to create an installable source distribution of your package. It looks in tox.ini for a list of environments and then for each environment...

1. tox creates a virtual environment in a .tox directory
2. tox pip installs some dependencies
3. tox pip installs your package from the sdist in step 1
4. tox runs your tests

After all of the environments are tested, tox reports a summary of how they all did.

```ini
tox.ini, put in same dir as setup.py

[tox]
envlist = py27, py36

[testenv]
deps=pytest
commands=pytest

[pytest]
addopts = -rsxX -l -tb=short --strict
markers =
smoke: Run the smoke test test functions
get: Run the test functions that test tasks.get()
```

### Jenkins CI: Automating Your Automated Tests

Continuous integration systems are frequently used to launch test suites after each code commit. pytest includes options to generate junit.xml-formatted files required by Jenkins and other CI systems to display test results.

When using Jenkins for running pytest suites, there are a few Jenkins plugins that you may find useful:

- build-name-setter
- Test Results Analyzer plugin

```bash
run_tests.bash

#!/bin/bash

# your paths will be different
top_path=/Users/okken/projects/book/bopytest/Book
code_path=${top_path}/code
venv_path=${top_path}/venv
tasks_proj_dir=${code_path}/$1
start_tests_dir=${code_path}/$2
results_dir=$3

# click and Python 3,
# from http://click.pocoo.org/5/python3/
export LC_ALL=en_US.utf8
export LANG=en_US.utf8

# virtual environment
source ${venv_path}/bin/activate

# install project
pip install -e ${tasks_proj_dir}

# run tests
cd ${start_tests_dir}
pytest --junit-xml=${results_dir}/results.xml
```

### unittest: Running Legacy Tests with pytest

pytest works as a unittest runner, and can run both pytest and unittest tests in the same session.

You can leave all the old tests as unittest, and write new ones in pytest.

You can also gradually migrate older tests.
There are a couple of issues that might trip you up in the migration.

Is possible to use the pytest session scope fixture with the unittest tests by adding @pytest.mark.usefixtures() decorators at the class or method level.

The ability to use marks has a limitation: you cannot use parametrized fixtures with unittest-based tests.

Another limitation with running unittest with pytest is that unittest subtests will stop at the first failure, while unittest will run each subtest, regardless of failures.
