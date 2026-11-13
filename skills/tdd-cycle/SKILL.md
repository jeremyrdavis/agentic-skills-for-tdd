---
name: tdd-cycle
description: 'Use when implementing or changing behavior in code that has, or should have, automated tests — the red-green-refactor loop. Trigger on a request to implement, add, or fix behavior, a diff that adds production code with no new or changed test, a test written after the code it covers, an expected value copied from a test run, several tests added before any of them passes, a test that passed without ever failing, or a refactoring mixed into the change that makes a test pass. Excludes writing the test list and choosing the next item (tdd-test-list), the test runner, tiers, stubs, doubles, and framework test annotations (the <stack>-tdd skill for your stack, e.g. quarkus-tdd), and exploratory spikes the user explicitly names as such.'
---

# TDD Cycle Skill

Red, green, refactor — one list item at a time, in any language. A test is written and seen to fail, the simplest change makes it and every earlier test pass, the design is tidied only as far as the next test needs, and the cycle repeats until the list (`tdd-test-list`) is empty. Design happens in two places and two hats: writing the test decides *how the behavior is invoked*; refactoring decides *how it is implemented*.

**Foundational principle.** TDD is a programming workflow whose product is confidence: after each cycle everything that used to work still works, the new behavior works as expected, the system is ready for the next change, and you can say why. Every rule below protects one of those four. Red before green proves the test can fail. An expected value derived by reasoning — never read off a run — is the double-check that gives the test its validation value. Green by the simplest change keeps the design driven by tests rather than by speculation. A separate refactoring hat keeps "make it run" and "make it right" from corrupting each other. A commit at every green is the rollback point that makes starting over cheap.

**Mode.** `TDD mode: auto` (default) runs the whole loop without pausing. `TDD mode: human` pauses for confirmation after the test list, before each green, and before each refactoring, and commits the red step separately. Announce the mode once at the start; switch whenever the user says "human mode" or "auto mode" and re-announce. Markers: `🔴` red, `🟢` green, `🧹` refactor.

**Red Flags — STOP if you find yourself thinking:**

- "I'll write all the tests first, then make them pass one by one."
- "It passed immediately — fine, moving on."
- "I'll run it and use the output as the expected value."
- "While I'm in here I'll clean this up" — while the test is still red.
- "This duplication needs an abstraction now."
- "I'll just drop that assertion; it's not important."
- "This is one line; it doesn't need a test."
- "The list is empty, so we're done" — while something about the behavior still worries you.

If any of these surface, re-read Core Rules and Excuse / Reality before typing.

---

## When to Use

- Implementing any new behavior, or changing existing behavior, in code with automated tests.
- Fixing a bug: the failing test that reproduces it is the first red.
- Reviewing a diff: production code without a test, a test added after the code, an expected value that looks pasted, a refactoring tangled with a behavior change.
- Deciding whether to stop: the list is empty, but is the fear gone?

**Out of scope**: writing and ordering the test list, ZOMBIES, and deciding the next item (use `tdd-test-list` — this skill takes the next item as given); the runner, test tiers and what "all tests" costs per tier, stub conventions, test doubles, and framework test annotations (use the `<stack>-tdd` skill for your stack, e.g. `quarkus-tdd`; if none is installed, use the project's runner and apply the rules with its equivalents); the shape of the interface a test invokes — that comes from the project's design conventions, whatever architecture it follows; this skill decides *when* the interface is decided, not what it looks like; exploratory spikes the user declares as such, which are thrown away, not merged.

---

## Core Rules

1. **One concrete test at a time, from the list.** Take the next item from `tdd-test-list`; write exactly one runnable test. Never convert the list into a batch of tests to be made green later — the first green changes decisions, and a batch means rework and no feedback until test six.
2. **A real test: setup, invocation, assertion.** Work backwards from the assertion. Separate given, when, and then with blank lines, not comments. A test without an assertion is coverage theater, not a test.
3. **The expected value is derived, never read.** Reason out what the result must be before running anything; write it down; then run. An expected value copied from a run's output turns the test into a tautology — it can only confirm what the code already does.
4. **Predict before every run.** Say what will fail and why (or that it will pass, and why) before running the tests. A wrong prediction is information; act on it before continuing.
5. **Red first, in two steps when something is new.** If the test names anything that does not exist yet, see the compile or resolution failure, add only what makes it compile — stubs without logic — and then see the assertion failure. If everything it names already exists, go straight to the assertion failure. Either way, the test is seen red before any behavior is written.
6. **A test that passes without ever being red is a stop.** Find which earlier step added the behavior ahead of its test, say so (`passed without red: <cause>`), record it in the commit message, and tighten the next green. Keep the test; it is now a regression test, but it did not drive anything.
7. **Green by the simplest change.** The smallest change that passes this test and every earlier one. Hard-coded returns are legitimate — the next test generalizes them. No refactoring while going green: two hats, one at a time.
8. **Discoveries go on the list, not into the code.** A case you notice while going green becomes a list item. If it invalidates work already done, revert to the last green commit and reorder the list rather than push through.
9. **Simplify on green.** For each line just added, ask "does a failing test require this?" Delete it, or add the list item that would. Run after each deletion.
10. **Refactor every cycle — consider, then do only what the next test needs.** Say what will change, or `🧹 none`, and why. No new behavior; tests green after each move; stop when the next test is easy to write. Duplication is a hint, not a command — do not abstract on the first repeat.
11. **Interface decisions belong to the test; implementation decisions belong to the refactor.** The test invokes whatever interface the project's design conventions call for — the loop is the same under any architecture — and that decision is made while writing the test, not before. The implementation behind it is this skill's: it changes shape only under green tests.
12. **Commit at every green and after each refactoring.** The message names the behavior. In human mode the red step is also committed, with the failing test alone, so the trail shows the test came first.
13. **Test behavior, not implementation.** Assert on returned values and resulting state. Never on which methods were called or in what order — such a test breaks on every refactoring and passes on every bug.
14. **Stop when the list is empty and no fear remains.** If anything about the behavior still worries you, it goes on the list and the loop continues. Push back when a request would skip the loop ("just add the feature"); the user may declare a spike explicitly, and a spike is thrown away.

For the canonical workflow this skill implements, its provenance, and what the skill adds beyond it, read `references/canon-tdd.md` — only when someone asks what TDD is or disputes a rule. For the worked red–green–refactor example in your stack — the runner, the compile-red stub, the tiers, the commit cadence — read `references/tdd-cycle.md` in the `<stack>-tdd` skill for your stack (e.g. `quarkus-tdd`), if installed. If none is installed, apply the rules above with the project's test runner; the loop is identical in every stack.

---

## Anti-patterns

| Don't | Why it's wrong | Fix |
| --- | --- | --- |
| Convert the whole list into tests, then make them pass | The first green changes decisions and the other tests are now wrong; no feedback until the batch is done. | One item, one test, one green (Rule 1). |
| A test with no assertion, "for coverage" | It cannot fail, so it proves nothing and will never be red. | Assert on the outcome; work backwards from it (Rule 2). |
| Run the code, paste the output as the expected value | The test now asserts whatever the code does, including the bug. | Derive the value by reasoning before the run (Rule 3). |
| Skip the red: write test and code together, run once, see green | No proof the test can fail; the test may be passing for the wrong reason. | See the failure first; two steps when something is new (Rule 5). |
| Ignore a test that passed immediately | It exercised behavior written ahead of its test — untested until now, and the drift is invisible. | Stop, name the cause, record it, tighten (Rule 6). |
| Tidy the code while the test is red | Two hats at once: when it fails you can't tell which change broke it. | Green first, then `🧹` (Rules 7, 10). |
| Implement the case you just noticed "while you're here" | Code without a driving test; the test that follows passes without red. | Append to the list (Rule 8). |
| Extract an abstraction at the first duplication | Two instances rarely show the real shape; the abstraction is speculation. | Wait for the hint to become a pattern (Rule 10). |
| Refactor beyond what the next test needs | It feels productive and delays the next red; the session ends in a big diff with no new behavior. | Only what the next test needs (Rule 10). |
| Verify calls on a mock instead of asserting an outcome | The test couples to the implementation; it breaks on refactoring and passes on bugs. | Assert state or result (Rule 13). |
| Report "all tests pass" after running only the new one | An earlier test may now fail; the claim is untested. | Run every previous test each green; the stack skill says what that costs per tier (Rule 7). |

---

## Excuse / Reality

When you catch yourself reasoning around the rules above, look here before you type. The left column is verbatim — what you'll actually say in your head or in a PR comment. The right column is what defeats it.

| Excuse | Reality |
| --- | --- |
| "Writing all the tests first is faster — I know what they are." | The first green will change a decision — a parameter, a return type, a name — and every unrun test is now wrong. Rework, plus no passing test for the whole batch. One test to green takes minutes and tells you whether the next one is still right. |
| "It passed immediately — that's a win, the code was already correct." | It means that code was written without a test, at some earlier step, and this is the first time anyone has looked. Call it out, find the step, and tighten. The transcript that records "passed without red" is what makes the drift fixable. |
| "I'll compute the expected value by running it; the arithmetic is tedious." | The arithmetic *is* the test. A value read from the output can only confirm the code, bugs included. If the value is hard to derive, that is a sign the test is too big — split the item. |
| "I'll clean this up while I'm in here; it's a two-line change." | Two hats. If the test fails afterwards you don't know whether the behavior change or the tidy-up broke it. Green first; the refactoring is one `🧹` later and gets its own commit. |
| "This duplication obviously needs an abstraction." | Two occurrences rarely reveal the real shape. Beck: duplication is a hint, not a command. Leave it until the third case or until the next test is hard to write *because* of it. |
| "The list is empty, so we're done." | The list holds what you thought of when you wrote it. Ask what you would still not bet on. If the answer is "nothing", you're done; if it is anything else, that is the next item. |

---

## Quick Reference

### The cycle

1. Take the next `[TEST]` item (`tdd-test-list`).
2. `🔴` Write one test; derive the expected value; predict the failure; see it fail (compile first if something is new).
3. `🟢` Simplest change that passes it and every earlier test; predict the pass; run; simplify; commit.
4. `🧹` Consider refactoring; say what or `none`; do only what the next test needs; run; commit.
5. Discoveries → list. Passed without red → stop and name it.
6. Repeat until the list is empty and nothing still worries you.

### Modes

| | `auto` (default) | `human` |
| --- | --- | --- |
| Pauses | none | after the list, before each green, before each refactoring |
| Commits | at every green and after each refactoring | the same, plus the red step with the failing test alone |
| Switch | say "human mode" | say "auto mode" |

### Companion skills

- **`tdd-test-list`** — step 1: the list, ZOMBIES, ordering, and the definition of done this skill checks against.
- **`<stack>-tdd`** — the mechanics skill for your stack (`quarkus-tdd`, `spring-tdd`, `pytest-tdd`, ...), if installed: the runner, what "all previous tests" costs per tier, the compile-red stub convention, doubles, and the worked example (`references/tdd-cycle.md` there). If none is installed, apply the rules above with the project's runner.

The loop does not depend on the architecture of the code: it is the same for Domain-Driven Design, for REST over Active Record, and for a script. The project's conventions decide what a test invokes; this skill decides when and in what order.
