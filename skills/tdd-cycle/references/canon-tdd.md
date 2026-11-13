# Canon TDD — the workflow the `tdd-*` skills implement

Load this file when someone asks what TDD actually is, or disputes a rule in
`tdd-cycle` or `tdd-test-list`. The rules in those files are sufficient for
doing the work; this file is their provenance.

## The five steps (Beck, 2023)

1. Write a list of the test scenarios you want to cover.
2. Turn exactly one item on the list into an actual, concrete, runnable test.
3. Change the code to make the test — and all previous tests — pass, adding items to the list as you discover them.
4. Optionally refactor to improve the implementation design.
5. Until the list is empty, go back to 2.

The intended outcome of a cycle is a new state of the system where everything that used to work still works, the new behavior works as expected, the system is ready for the next change, and the programmer and their colleagues are confident of all three.

`tdd-test-list` is step 1 and the choice of the next item; `tdd-cycle` is steps 2–5.

## Two kinds of design, two steps

Beck separates *how a piece of behavior is invoked* from *how the system implements it*. Writing the test (step 2) is where interface decisions are made; refactoring (step 4) is where implementation decisions are made. Mixing them is the "two hats" problem.

This split is also why the loop is the same under any architecture: whatever the project's conventions prescribe for how behavior is invoked is settled while writing the test; the implementation behind it is still discovered test by test and tidied in the refactor step.

## The mistakes Beck names

| Step | Mistake |
| --- | --- |
| Test list | Mixing implementation design decisions into the list. |
| Write a test | Tests without assertions, written for coverage. |
| Write a test | Converting every item on the list into a concrete test, then making them pass one at a time — rework when the first green changes a decision; no feedback until test six. |
| Make it pass | Deleting assertions so the test pretends to pass. |
| Make it pass | Copying actual, computed values into the expected values of the test — "that defeats double checking, which creates much of the validation value of TDD". |
| Make it pass | Mixing refactoring into making the test pass. Make it run, then make it right. |
| Refactor | Refactoring further than this session needs. |
| Refactor | Abstracting too soon — "duplication is a hint, not a command". |

Picking the next test is a skill; the order affects both the experience and the result. If a discovery while going green invalidates the work so far, start over in a different order rather than push through.

## Fowler's framing

Writing the test first yields self-testing code, and forces thinking about the interface before the implementation. The test list comes first; sequencing the tests is a skill — "pick tests that drive us quickly to the salient points in the design". The most common way to get TDD wrong is neglecting the refactoring step: without it the result is "a messy aggregation of code fragments" — tested, but messy.

## What `tdd-cycle` adds beyond canon, and why

Canon is written for people. The additions below exist because an assistant that can execute code has failure modes a person does not, and because each has evidence behind it.

- **Predict before every run** (Rule 4) and **derive the expected value by reasoning** (Rule 3): the assistant can run the code and read the answer off the output, which is exactly the double-checking loss Beck describes.
- **Two-step red** (Rule 5): a compile or resolution failure first proves the test names something new; the assertion failure then proves the test can fail. Cheap, and it exposes the next item.
- **Passed-without-red callout** (Rule 6): the symptom of implementing ahead of the test; naming it keeps the drift visible instead of silent.
- **Simplify on green** (Rule 9): "does a failing test require this line?" — the assistant's version of procrastinating deliberately.
- **Commit at every green and after each refactoring** (Rule 12): Beck's "start over in a different order" presumes a cheap rollback point.

## Sources

- Kent Beck, *Canon TDD*, Software Design: Tidy First?, 11 December 2023 — https://newsletter.kentbeck.com/p/canon-tdd
- Martin Fowler, *Test Driven Development*, martinfowler.com bliki, revised 11 December 2023 — https://martinfowler.com/bliki/TestDrivenDevelopment.html
- Kent Beck, *Test-Driven Development: By Example*, Addison-Wesley, 2002.
