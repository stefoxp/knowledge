# Patterns for Test-Driven Development

## TDD Patterns

### Test (noun)

Write an automated test.

With automated tests, when I start to feel stress, I run the tests.

### Isolated Test

- make the tests so fast to run that I can run them myself, and run them often
- a huge stack of paper didn't usually mean a huge list of problems. More often it meant that one test had broken early, leaving the system in an unpredictable state for the next test

> Tests should be able to ignore one another completely

Implications of isolated tests:

1. the tests are order independent
2. you have to work to break your problem into little orthogonal dimensions, so setting up the environment for each test is easy and quick

### Test List

Before you begin, write a list of all the tests you know you will have to write.

- put on the list examples of every operation that you know you need to implement
- for those operations that don't already exist, put the null version of that operation on the list
- list all of the refactorings that you think you will have to do in order to have clean code at the end of this session

As you make the tests run, the implementation will imply new tests.
Write the new tests down on the list.
Likewise with refactorings.

### Test First

You should write your tests before you write the code that is to be tested.

When we test first, we reduce the stress, which makes us more likely to test.

### Assert First

Try writing the asserts first.
This technique has a powerful simplifying effect.

### Test Data

Use data that makes the tests easy to read and follow.

You can use **Realistic Data**  when:

- you are testing real-time systems
- you are matching the output of the current system with the output of a previous system (parallel testing)
- you are refactoring a simulation and expect precisely the same answers when you are finished

### Evident Data

Include expected and actual results in the test itself, and try to make their relationship apparent.

One beneficial side effect is that it makes programming easier.

## Red Bar Patterns

### One Step Test

Pick next from the list a test that will teach you something and that you are confident you can implement.

If you don't find any test on the list that represents one step, then add some new tests that would represent progress toward the items there.

### Starter Test

Start by testing a variant of an operation that doesn't do anything.

### Explanation Test

Ask for and give explanations in terms of tests.

### Learning Test

Write tests for externally produced software before the first time you are going to use a new facility in the package.

If your understanding of the API is correct, then the test will pass first time.

### Another Test

When a tangential idea arises, add a test to the list and go back to the topic.

### Regression Test

When a defect is reported, write the smallest possible test that fails and that, once run, will be repaired.

... are test that, with perfect foreknowledge, you would have written when coding originally.

### Break

When you feel tired or stuck, take a break.

Additional outside elements:

- keep a water bottle by your keyboard so that biology provides the motivation for regular breaks
- committments after regular work hours can help you to stop when you need sleep before progress
- weekend commmitments help get your conscious, energy-sucking thoughts off work
- mandatory vacation policies help you refresh yourself completely

### Do Over

When you are feeling lost, throw away the code and start over.

### Cheap Desk, Nice Chair

Get a really nice chair.

Get cheap / slow / old machines for individual e-mail and surfing, and the hottest possible machines for shared development.

## Testing Patterns

### Child test

Write a smaller test case that represents the broken part of the bigger test case.
Get the smaller test case running.
Reintroduce the larger test case.

### Mock Objects

- [http://www.mockobjects.com/](http://www.mockobjects.com/)

How do you test an object that relies on an expensive or complicated resource?

Create a fake version of the resource that answers constants.

The classic example is database:

- it take a long time to start
- it is difficult to keep clean
- it is located on a remote server
- it is also a fertile source of error in development

The solution is not to use a real database most of the time.

```java
public void testOrderLookup() {
    // db sitting in memory
    Database db = new MockDatabase();
    // if it does not get the query it expects, then it throws an exception
    db.expectQuery("select order_no from Order where cust_no is 123");
    // if the query is correct, then it returns something that looks like a result set constructed from the constant strings
    db.returnResult(new String[] {"Order 2", "Order 3"});
}
```

Another value of mocks, aside from **performance** and **reliability**, is **readability**.

... encourage you down the path of carefully considering the visibility of every object, reducing the coupling in your designs.

### Self Shunt

How do you test that one object communicates correctly with another?

Have the object under test communicate with the test case instead of with the object it expects.

Tests written with Self Shunt tend to read better than tests written without it.

### Log String

Ho do you test that the sequence in which messages are called is correct?

Keep a log in a string, and append to the string when a message is called.

Work well with Self Shunt.

### Crash test dummy

How do you test error code that is unlikely to be invoked?

Invoke it anyway with a special object that throws an exception instead of doing real work.

Code that isn't tested doesn't work.

... is lik a Mock Object, except you don't need to mock up the whole object.

### Broken test

When you are programming alone, leave the last test broken.

When you come back to the code, you then have an obvious place to start.

### Clean check-in

When you are programming in a team, leave all the tests running.

## Green Bar Patterns

Once you have a broken test, you need to fix it. If you treat a red bar as a condition to be fixed as quickly as possible, then you will discover that you can get to green quickly.

### Fake it

The first implementation once you have a broken test is: return a constant.

Once you have the test running, gradually transform the constant into an expression using variables.

There are a couple of effects that make Fake it powerful:

- **Psychological** Having a green bar feels completely different from having a red bar. When the bar is green you can refactor from there with confidence.
- **Scope control** Programmers are good at imagining all sorts of future problems. Starting with one concrete example and generalizing from there prevents you from prematurely confusing yourself with extraneous concerns.

### Triangulate

Abstract only when you have two or more examples.

Triangulation is attractive because the rules for it seem so clear.

Although they seem simple, the rules for Triangulation create an infinite loop.

I use Triangulation when I'm really, really unsure about the correct abstraction for the calculation. Otherwise I rely on either Obvious Implementation or Fake it.

### Obvious implementation

Simple operations: implement them.

### One to Many

Implement an operation that works with collections of objects without the collection first, then make it work with collections.

## xUnit Patterns



## Design Patterns

## Refactoring

## Mastering TDD