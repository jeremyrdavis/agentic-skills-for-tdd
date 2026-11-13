# ZOMBIES — Test Case Discovery Heuristic

Load this file while writing or checking a test list (`tdd-test-list` Rule 3).
It is a completeness check, not a procedure: walk the letters, add the items
the list is missing, then return to the list.

## Structure

**ZOM axis** (simple → complex):

- **Z — Zero**: the initial state after creation: empty, not full, default values, nothing has happened yet.
- **O — One**: the first item, the first transition, the first call that changes state.
- **M — Many**: several items, repeated transitions, the scenarios that need a loop or a collection.

**BIE considerations** (apply at every ZOM level):

- **B — Boundary**: transitions between states, in both directions (empty ↔ not-empty, not-full ↔ full, below ↔ at ↔ above a limit).
- **I — Interface**: let the tests reveal what methods, parameters, and return types are needed; the test is where the interface is decided.
- **E — Exceptions**: error conditions — and that the object still works after a failed operation.

**S — Simple scenarios, simple solutions** (throughout): the simplest test that forces new behavior, and the simplest change that passes it.

## Key principles

- **Test transitions, not just states.** Verify moving from empty to not-empty, and back again.
- **Procrastinate deliberately.** Defer implementation until a test demands it. A hard-coded return value is fine; the next test catches it if you forget to generalize.
- **The interface emerges from tests.** Don't design the API up front; write the test and the needed call reveals itself. When another skill constrains the interface (an aggregate's factory and transition methods, for instance), the test invokes that interface — ZOMBIES decides which *cases* to cover, not what the API looks like.
- **Exceptions come last.** Get the happy paths working first, then test the error conditions, including that a failed operation leaves the object usable.

## Example: circular buffer (FIFO)

```
[TEST] New buffer is empty                               <- Z
[TEST] New buffer is not full                            <- Z
[TEST] Put one item, buffer is not empty                 <- O
[TEST] Put then get returns the item                     <- O + I
[TEST] Put then get, buffer is empty again               <- O + B (transition back)
[TEST] Put three items, get returns them in order        <- M
[TEST] Fill to capacity, buffer is full                  <- M + B
[TEST] Wrap around: fill, empty, refill works            <- M + B
[TEST] Put to full buffer fails                          <- E
[TEST] Get from empty buffer fails                       <- E
[TEST] After failed put, buffer still works              <- E (integrity check)
```

The order above is also a good implementation order: Z items first, exceptions last, boundaries as soon as the state they cross exists.

---

Source: *TDD Guided by ZOMBIES* by James Grenning — https://blog.wingman-sw.com/tdd-guided-by-zombies. This adaptation follows the structure of the `tdd` skill in https://github.com/lexler/rpg-combat-tdd-vs-habit-hooks.
