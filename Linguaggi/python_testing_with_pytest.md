# Python Testing with pytest

Brian Okken

- [Book reviews on GoodReads.com](https://www.goodreads.com/book/show/34695799-python-testing-with-pytest)

## Writing Test Functions

### Project task_proj

Before to run pytest you should install locally tasks using pip.

If you want to be able to modify the source code while tasks is installed, you need to install it with the -e option:

>pip install -e ./tasks_proj/

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
