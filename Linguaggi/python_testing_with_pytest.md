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
