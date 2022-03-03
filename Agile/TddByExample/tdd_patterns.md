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

## Green Bar Patterns

## xUnit Patterns

## Design Patterns

## Refactoring

## Mastering TDD