---
name: tdd-test-list
description: 'Use when starting a behavior change and no test list exists, when choosing the next test to write, or when reviewing tests for gaps. Trigger on a feature, story, or bug with no enumerated cases, a request to "write tests for" or "what tests am I missing", a [TEST] list that contains implementation notes (class names, data structures, algorithms), a test suite with only happy paths, a list that starts with the hardest case or with error handling, or a next test that forces no new behavior. Excludes the red-green-refactor loop itself (tdd-cycle) and the test runner, tiers, doubles, and framework test annotations (the <stack>-tdd skill for your stack, e.g. quarkus-tdd).'
---

# TDD Test List Skill

The first step of TDD, in any language: before a test, before a line of code, a list of the behaviors the change must exhibit — and the ones it must not break. One line per behavior, phrased as an observable outcome. The list is the frame the red–green–refactor loop (`tdd-cycle`) runs inside: one item becomes one test, the list grows as going green reveals cases, items are marked off at green, and an empty list is the definition of done.

**Foundational principle.** The list is behavioral analysis, not design. It answers "in which situations must this behavior work, and what must still work afterwards?" — never "which classes will I write?" It is alive: it is appended during the loop and shrinks only as tests go green. Order matters as much as content: the first test should force new behavior with the least machinery, and the sequence should reach the design's salient points soon. Exceptions come last. Finishing is not "the list is empty"; it is "the list is empty **and nothing about the behavior still worries me**."

**Red Flags — STOP if you find yourself thinking:**

- "I'll sketch the classes first, then list the tests." (The list is about behavior; the classes come from the tests.)
- "The list is complete" — before walking ZOMBIES.
- "Exceptions first, they're the interesting part." (Happy paths first; errors once the object exists to fail.)
- "Start with the hardest case so the design is right from the beginning." (Start with the simplest case that forces new behavior.)
- "This case is obvious, it can go straight into the code." (Onto the list. Obvious cases are where untested code lives.)
- "I'll write the list in my head." (If it isn't in the file, it won't be maintained.)

If any of these surface, re-read Core Rules and Excuse / Reality before typing.

---

## When to Use

- Starting any behavior change — a feature, a story, a bug fix — before the first test.
- Choosing which item to turn into the next test.
- Mid-loop, when making a test pass reveals a case that was not on the list.
- Reviewing a test suite for gaps: missing zero/one/many cases, missing boundary transitions, only happy paths.
- Reviewing a `[TEST]` list for implementation leakage or bad ordering.

**Out of scope**: turning an item into a test and making it pass — the loop, its red and green discipline, and refactoring (use `tdd-cycle`); where the list physically lives in a given stack, the test runner, test tiers, and doubles (use the `<stack>-tdd` skill for your stack, e.g. `quarkus-tdd`; if none is installed, keep the list as comments at the bottom of the test file); the design of the interface the tests will invoke (that is decided while writing each test, constrained by design skills such as `ddd-*` when installed).

---

## Core Rules

1. **The list comes first.** Before any test or production code, write the list. One line per behavior, phrased as an observable outcome: `[TEST] New buffer is empty`, `[TEST] Cancelling a fulfilled order is refused`. Keep it in the test file as `[TEST]` comments (or wherever `<stack>-tdd` says) so it is versioned with the code.
2. **Behavior only.** No class names, data structures, or algorithms on the list. If a line says *how*, rewrite it to say *what is observable*.
3. **Walk ZOMBIES before calling the list complete.** Zero, One, Many; at each level Boundary, Interface, Exceptions; Simple scenarios, simple solutions. The heuristic and its worked example are in `references/zombies.md`.
4. **List what must still work.** Existing behavior the change could break gets its own items ("still works" cases), even if a test already exists — the item says the suite must stay green for that reason.
5. **Order the list.** First: the simplest item that forces new behavior. Then Z → O → M; boundaries as soon as the states they cross exist; exceptions last. Among equals, prefer the item that reaches a salient design decision soonest.
6. **Append as you go.** A case discovered while making a test pass goes on the list — never straight into the code. If the discovery invalidates work already done, the loop reverts and the list is reordered (`tdd-cycle` Rule 8).
7. **Mark off only at green.** An item leaves the list when its test passes and is committed. Reorder freely at any time. An item that turns out redundant is removed with a one-line reason, not silently.
8. **One behavior per item.** If an item needs "and", split it. If an item needs "unless", the exception is a separate item, later in the list.
9. **Done = empty list + no remaining fear.** The closing review asks "what would I still not bet on?" and puts the answer on the list. When nothing comes back, the change is done.

For the heuristic in full — the ZOM axis, the BIE considerations, the key principles, and an annotated example list — read `references/zombies.md`. Load it while writing or checking a list; the rules above are sufficient for ordering and review decisions. A stack skill (`<stack>-tdd`) says where the list lives and how items map to test tiers; if none is installed, keep the list as `[TEST]` comments in the test file.

---

## Anti-patterns

| Don't | Why it's wrong | Fix |
| --- | --- | --- |
| `[TEST] OrderRepository caches by id` | Implementation on the list; it commits to a design before any test has forced it. | `[TEST] Loading the same order twice returns equal orders` (Rule 2). |
| A list with no transition items (`empty → not empty`, `not full → full`, and back) | States get tested, transitions don't; the bugs are in the transitions. | Add a Boundary item at each ZOM level, both directions (Rule 3). |
| A list that is all happy paths | Error conditions are discovered in production. | Walk E: each failure mode, plus "still works after the failure" (Rule 3). |
| A list that starts with the error cases | The object doesn't exist yet to fail; the first tests build scaffolding instead of behavior. | Exceptions last (Rule 5). |
| A list that starts with the hardest case | Big first step, slow first green, design decided under maximum uncertainty. | Simplest item that forces new behavior first (Rule 5). |
| A case discovered mid-green implemented on the spot | Untested code, and the test for it — if it ever comes — passes without red. | Append to the list; finish the current item (Rule 6). |
| Declaring done because the list is empty | The list only holds what you thought of. | Ask what you still wouldn't bet on; add it (Rule 9). |

---

## Excuse / Reality

When you catch yourself reasoning around the rules above, look here before you type. The left column is verbatim — what you'll actually say in your head or in a PR comment. The right column is what defeats it.

| Excuse | Reality |
| --- | --- |
| "I know the design already; listing tests first is ceremony." | Then the list takes two minutes and costs nothing. When the design is wrong, the list is the only artifact that shows it — the first test that is awkward to write is the design telling you so, before the code exists. |
| "I'll put the implementation notes on the list so I don't forget them." | Then the tests get written to fit the notes, and the implementation is decided before a test has forced anything. Keep a separate scratch note if you must; the list is behavior only. |
| "Exceptions are where the bugs are, so they go first." | Exceptions need an object that works first; testing failure before success means building the happy path untested, inside the error test's setup. Happy paths first, then every failure mode, then "still works afterwards". |
| "The hardest case drives the real design, so start there." | It also takes the longest to get green and decides the design when you know the least. The simplest forcing case gets a design decision made in minutes, with a green test to protect it. |
| "The list is a plan; plans go stale, so I stopped updating it." | The list is the definition of done. A stale list means done is undefined — and the cases you discovered on the way live only in your head. Appending a line costs seconds. |
| "ZOMBIES is for katas; real features don't fit it." | Every real feature has an initial state, a first transition, and repetition; every real feature has limits and failure modes. If a letter truly doesn't apply, say which and why — that takes one line and is itself a review record. |

---

## Quick Reference

### ZOMBIES completeness check

- [ ] **Z**ero: initial state — empty, default, nothing happened yet.
- [ ] **O**ne: first item, first transition.
- [ ] **M**any: several items, repeated transitions.
- [ ] **B**oundary at each level, both directions.
- [ ] **I**nterface: each item is written as a call a test could make.
- [ ] **E**xceptions last, plus "still works after the failure".

### List lifecycle

- [ ] Written before any test or code; `[TEST]` lines, behavior only.
- [ ] Includes "still works" items for behavior the change could break.
- [ ] Ordered: simplest forcing case → Z → O → M → exceptions.
- [ ] Appended when going green reveals a case; never implemented on the spot.
- [ ] Items marked off only at green; removals carry a reason.
- [ ] Empty, and nothing still worries you → done.

### Companion skills

- **`tdd-cycle`** — turns one item into one test and makes it pass; Rule 8 there is the other half of Rule 6 here.
- **`<stack>-tdd`** — the mechanics skill for your stack (`quarkus-tdd`, `spring-tdd`, `pytest-tdd`, ...), if installed: where the list lives, which test tier each item maps to, the runner. If none is installed, keep the list as comments in the test file and use the project's runner.
- **`ddd-*`** (if installed) — the vocabulary the items use: aggregate behaviors and invariants, value-object validation, events raised. The list describes behavior in the ubiquitous language; it never names layers or persistence.
